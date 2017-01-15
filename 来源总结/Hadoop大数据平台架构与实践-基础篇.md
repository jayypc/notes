### 初识hadoop

#### 要求

- 掌握大数据存储与处理技术的原理（理论知识）
- 掌握hadoop的使用和开发能力（实践能力）

#### 出现原因

- 无处不在的大数据（科学数据、物联网数据、交通数据、社交网络数据、零售数据、金融数据）
- 如何对大数据进行存储与分析呢？
  - 系统瓶颈：存储容量、读写速率、计算效率等

#### 谷歌的应对方案

- MapReduce、BigTable、GFS（仅提出大数据技术论文）
  - 成本降低，能用pc机，就不用大型机和高端存储
  - 软件容错硬件故障视为常态，通过软件保证可靠性
  - 简化并行分布式计算，无须控制节点同步和数据交换

#### hadoop是模仿谷歌大数据技术的开源实现

- 是什么？开源的、分布式存储  +  分布式计算平台
- 核心组成：
  - HDFS：分布式文件系统，存储海量数据
  - MapReduce：并行处理框架，实现任务分解和调度
- 能做什么？搭建大型数据仓库，PB级数据的存储、处理、分析、统计等业务
- 优势：
  - 高扩展
  - 低成本
  - 成熟的生态圈（开源的原因）
    - Hive：将sql语句转换为hadoop任务去执行，降低使用hadoop的门槛
    - Hbase：存储结构化数据的分布式数据库，同传统的关系型数据库区别是Hbase放弃事物特性追求更高的扩展；同HDFS不同的是Hbase提供数据的随机读写和实时访问，实现对表数据的读写功能
    - zookeeper：监控hadoop集群里面每个节点的状态，管理整个集群的配置，维护节点间数据一致性等
- 人才需求：hadoop开发、hadoop运营（对一个大规模hadoop集群，能够hadoop性能优化等）

### 安装hadoop

#### 安装jdk

hadoop的运行需要jdk：因为hadoop是用java语言来写的

```shell
vim /etc/profile

配置环境变量
export JAVA_HOME=/jdk路径
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/lib:$JRE_HOME/lib:$PATH

让配置生效
source /etc/profile
```

#### 配置hadoop

- conf
  - hadoop-env.sh
  - core-site.xml
  - hdfs-site.xml
  - mapred-site.xml
- bin
  - start-all.sh（启动）

### HDFS简介


- HDFS的文件被分成块（默认64MB）进行存储，块是文件存储处理（备份、查找）的逻辑单元

#### HDFS有两类节点

-   NameNode是管理节点，存放文件元数据
    - 文件与数据块的映射表
    - 数据块与数据节点的映射表
-   DateNode是工作节点，存放数据块

    ![hadoop1](https://raw.githubusercontent.com/jayypc/notes/master/images/hadoop1.png)

#### HDFS中数据管理与容错

- 每个数据块都有3个副本，分布在两个机架内的三个节点
- 心跳检测：NameNode和DateNode之间有心跳协议，DateNode定期向NameNode发送心跳消息
    - 二级NameNode：SecondaryNameNode会定期同步元数据映像文件和修改日志，一旦 NameNo的发生故障，会替代NameNode

#### HDFS中文件的读写操作

- 读：
    - 客户端（java程序或者命令行）发起读取请求给NameNode
    - NameNode返回该文件在哪些DateNode的哪个块上面
    - 客户端根据这些信息下载块，然后组装
- 写：
    - 客户端将文件拆分成块后，通知NameNode
    - NameNode返回当前在线的可用的足够磁盘空间的DataNodes
    - 客户端对块进行写入，然后对块进行流水线复制
    - 最后通知NameNode更新元数据

#### HDFS的特点

- 数据冗余，硬件容错
- 流式的数据访问
    - 一次写入多次读取
    - 一旦写入之后不会修改也无法修改
- 存储大文件
    - 如果是小文件NameNode的负责压力会很大

#### HDFS的适用性和局限性

- 适合数据批量读写，吞吐量高
- 不适合交互式应用，低延迟很难满足
- 不支持多用户并发写相同文件

#### HDFS的命令行使用

```shell
使用之前对namenode格式化操作
hadoop namenode -format

fs命令
hadoop fs -ls /
打印根目录文件

hadoop fs -mkdir input
新建目录(会生成在/user/root下)

hadoop fs -put xxx.txt input/
提交文件

hadoop fs -cat input/xxx.txt
查看文件

hadoop fs -get input/xxx.txt yyy.txt
获取文件，同时重命名

hadoop dfsadmin -report
查看文件系统的所有信息(包括使用空间、多少个块等信息)
```

### MapReduce原理

一个大任务分成多个小的子任务（map），**并行**执行后，合并结果（reduced）

![hadoop2](https://raw.githubusercontent.com/jayypc/notes/master/images/hadoop2.png)

#### 基本概念

- Job & Task
  - 一个作业（Job）会被拆分成多个任务（Task），Task中又分MapTask、ReduceTask

#### MapReduce体系机构中有两个节点

- JobTracker
  - 作业调度
  - 分配任务、监控任务执行进度
  - 监控TaskTracker的状态
- TaskTracker
  - 执行任务
  - 汇报任务状态

![hadoop3](https://raw.githubusercontent.com/jayypc/notes/master/images/hadoop3.png)

#### MapReduce的容错机制

- 重复执行：重复4次以后还是失败则放弃执行
- 推测执行：
  - 对执行慢的TaskTracker，在新的机器上面新建同样的TaskTracker，一旦完成结束另一任务
  - 保证了整个任务的计算不会因为某一两个的TaskTracker故障等问题导致执行效率底

### 开发hadoop程序

#### WordCount实例

```shell
编译java文件，由于类里引用了hadoop的包，所以要指明路径
javac -classpath /opt/hadoop-1.2.1/hadoop-core-1.2.1.jar:/opt/hadoop-1.2.1/lib/commons-cli-1.2.jar -d word_count_class WordCount.java

进入目录word_count_class
打包jar包
jar -cvf wordcount.jar *.class

提交任务
hadoop jar word_count_class/wordcount.jar WordCount input_wordcount output_wordcount
其中WordCount指明main函数所在类
output_wordcount指明输出结果文件夹，如果hadoop里面没有这个文件夹，则会自动创建
```

#### 利用MapReduce进行排序

> 完