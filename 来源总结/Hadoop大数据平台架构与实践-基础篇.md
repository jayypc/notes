### 初识hadoop

- 要求
  - 掌握大数据存储与处理技术的原理（理论知识）
  - 掌握hadoop的使用和开发能力（实践能力）
- 出现原因
  - 无处不在的大数据（科学数据、物联网数据、交通数据、社交网络数据、零售数据、金融数据）
  - 如何对大数据进行存储与分析呢？
    - 系统瓶颈：存储容量、读写速率、计算效率等
- 谷歌的应对方案（提出大数据技术论文）
  - MapReduce、BigTable、GFS
    - 成本降低，能用pc机，就不用大型机和高端存储
    - 软件容错硬件故障视为常态，通过软件保证可靠性
    - 简化并行分布式计算，无须控制节点同步和数据交换
- hadoop是模仿谷歌大数据技术的开源实现
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

- 安装jdk

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

- 配置hadoop
  - conf
    - hadoop-env.sh
    - core-site.xml
    - hdfs-site.xml
    - mapred-site.xml
  - bin
    - start-all.sh（启动）

### HDFS简介

- HDFS的文件被分成块（默认64MB）进行存储，块是文件存储处理（备份、查找）的逻辑单元

- HDFS有两类节点：

  - NameNode是管理节点，存放文件元数据
    - 文件与数据块的映射表
    - 数据块与数据节点的映射表
  - DateNode是工作节点，存放数据块

  ![hadoop1](..\images\hadoop1.png)

- HDFS中数据管理与容错

  - ​