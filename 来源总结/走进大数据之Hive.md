### 概述

#### 数据仓库

- 面向主题的、集成的、不可更新的、随时间不变化的数据集合
- 用于支持企业或组织的决策分析处理

#### 数据仓库的结构和建立过程

- 数据源：业务数据系统、文档资料（csv或txt等）、其他数据
- 数据存储及管理：
  - 抽取（Extract）
  - 转换（Transform）
  - 装载（Load）
- 数据仓库引擎
  - 服务器
- 前端展示

#### OLTP应用与OLAP应用

- on-line transaction processing应用：是传统的关系型数据库的主要应用，主要是基本的、日常的事务处理，例如银行交易
- On-Line Analytical Processing应用：是数据仓库系统的主要应用，支持复杂的分析操作，侧重决策支持，并且提供直观易懂的查询结果，例如商品推荐系统

#### 数据仓库中的数据模型

- 星星模型
- 雪花模型

#### 什么是Hive

- Hive是构建在hadoop HDFS上的一个数据仓库（区别像oracle数据库的数据是存在oracle里面的）
- Hive可以用来进行数据提取转换加载（ETC）
- Hive定义了简单的类似SQL查询语言，称为HQL它允许熟悉SQL的用户查询数据
- Hive有HQL解析引擎，它将HQL语句转换成M/R Job然后在hadoop执行
- Hive允许熟悉MapReduce开发者的开发自定义的mapper和reducer来处理内建的mapper和reducer无法完成的复杂分析工作
- Hive的表其实就是HDFS的目录，Hive的数据就是HDFS中的文件

### Hive的体系结构

#### Hive的元数据

- Hive将元数据存储在数据库中，支持mysql、derby、oracle等数据库。默认derby
- 元数据包括表的名字，表的列和分区及其属性，表的属性（是否为外部表等），表的数据所在目录等

![Hive1](https://raw.githubusercontent.com/jayypc/notes/master/images/Hive1.png)

#### HQL解析引擎

- 解析器（语法分析）、编译器（生成HQL的查询计划）、优化器（生成最佳的查询计划），生成的查询计划存储在HDFS中，并随后又MapReduce调用执行

#### 整体体系结构图

![Hive2](https://raw.githubusercontent.com/jayypc/notes/master/images/Hive2.png)

### Hive的安装

#### 安装模式

- 嵌入模式
  - 元数据信息被存在Hive自带的derby数据库中
  - 只允许创建一个连接
  - 多用于demo
- 本地模式
  - 元数据信息被存在MySQL数据库中
  - MySQL数据库与Hive运行在同一台物理机器上
  - 多用于开发和测试
- 远程模式
  - 元数据信息被存在MySQL数据库中
  - MySQL数据库与Hive运行在不同物理机器上
  - 多用于实际开发

#### 安装过程

- 嵌入模式：无需安装解压后就可以使用，只需配置bin/hive到环境变量中

- 远程模式

  - 解压

  - 上传MySQL驱动jar到解压路径lib下

  - 更改配置conf

    - 创建cp hive-default.xml.template hive-site.xml

    ```xml
    <?xml version="1.0"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

    <configuration>
    	<property>
          <name>hive.metastore.warehouse.dir</name>
          <value>/opt/hive/warehouse</value>
      	</property>
      	<property>
          <name>hive.metastore.local</name>
          <value>true</value>
      	</property>
      	<!-- 如果是远程mysql数据库的话需要在这里写入远程的IP或hosts -->
      	<property>
          <name>javax.jdo.option.ConnectionURL</name>
          <value>jdbc:mysql://node5:3306/hive?createDatabaseIfNotExist=true</value>
      	</property>
      	<property>
          <name>javax.jdo.option.ConnectionDriverName</name>
          <value>com.mysql.jdbc.Driver</value>
      	</property>
      	<property>
          <name>javax.jdo.option.ConnectionUserName</name>
          <value>root</value>
        </property>
        <property>
          <name>javax.jdo.option.ConnectionPassword</name>
          <value>123456</value>
      	</property>
    </configuration>
    ```

- 命令


```shell
登陆mysql，在cmd下运行
mysql -u用户名 -p密码

创建一个数据库
create database hive

配置完Hive后，运行linux上面hive命令
创建一个hive表test1,tid是id名，tname是列名，后面是类型
create table test1(tid int,tname string)
```

### Hive的管理

#### Hive的启动方式

- CLI（命令行）方式

  - 直接运行$HIVE_HOME/bin/hive（hive -S 静默模式 hive -e ‘语句’ 直接执行一条语句）
  - hive --service cli

  ```shell
  清屏
  Ctrl+L 或者 !clear

  查看数据仓库中的表
  show tables;

  查看数据仓库中内置的函数
  show functions;

  查看表的结构
  desc 表名

  查看HDFS上的文件
  dfs -ls  目录
  dfs -lsr 递归的方式打印指定目录下所有子目录

  执行Linux操作系统的命令
  ! 命令

  执行HQL语句
  select *** from ***

  执行SQL的脚本
  source SQL文件
  ```

- Web界面方式

  - hive --service hwi（默认端口9999）

    - 需要将源代码中hwi中web文件夹打包成war包后，复制到hive目录的lib文件夹中

    - 修改配置文件，追加内容

      ```xml
      <property>
        <name>hive.hwi.listen.host</name>
        <value>0.0.0.0</value>
        <description>This is the host address the Hive Web Interface will listen on</description>
      </property>
      <property>
        <name>hive.hwi.listen.port</name>
        <value>9999</value>
        <description>This is the port the Hive Web Interface will listen on</description>
      </property>
      <property>
        <name>hive.hwi.war.file</name>
        <value>${HIVE_HOME}/lib/hive-hwi-<version>.war</value>
        <description>This is the WAR file with the jsp content for Hive Web Interface</description>
      </property>
      ```

  - ctrl + c 结束这个服务

- 远程服务启动方式

  - hive --service hiveserver（默认端口10000）
  - 以JDBChuoODBC的程序登陆到hive中操作数据时，必须选用远程服务启动方式

### Hive的数据类型

#### 基本数据类型

- tinyint(-128 to 127)/smallint(-32,768 to 32,767)/int/bigint：整数类型
- float/double：浮点数类型
- boolean：布尔类型
- string：字符串类型
  - varchar(20)：最大是20字符，但可能小于20字符
  - char(20)：不管存储的是多少字符，按照20字符来存储

#### 复杂数据类型

- Array：数组类型，由一系列相同数据类型的元素组成

  ```sql
  create table student
  (sid int,
   sname string,
   grade array<float>);
  示例数据：{1,Tom,[80,90,75]}
  ```

- Map：集合类型，包含key->value键值对，可以通过key来访问元素

  ```sql
  create table student
  (sid int,
   sname string,
   grade map<string,float>);
  示例数据：{1,Tom,<'大学语文'，85>}

  create table student
  (sid int,
   sname string,
   grade array<map<string,float>>);
  示例数据：{1,Tom,[<'大学语文'，85>,<'大学数学'，95>]}
  ```

- Struct：结构类型，可以包含不同数据类型的元素。这些元素可以通过“点语法“的方式来得到所需值

  ```sql
  create table student
  (sid int,
   info struct<name:string,age:int,sex:string>);
  示例数据：{1,{'Tom',10,'男'}}
  ```

#### 时间数据类型

- Date：日期，不包含时间（从Hive0.12.0开始支持）

- Timestamp：与时区无关的，存储的是偏移量（从Hive0.8.0开始支持）

  ```sql
  Hive查看当前时间戳
  select unix_timestamp();

  cast() 函数可以对Date、Timestamp、string等互相转换
  ```

### Hive的数据模型

#### Hive的数据存储

- 基于HDFS
- 没有专门的数据存储格式
- 存储结构主要包括：数据库、文件、表、视图
- 可以直接加载文本文件（.txt .csv文件等）
- 创建表时，指定Hive数据的列分隔符与行分隔符

#### 表

- Table 内部表

  - 与数据库中的Table在概念上是类似

  - 每个Table在Hive中都有一个相应的目录存储数据

  - 所有Table数据（不包括External Table）都保存在这个目录

  - 删除表时，元数据与数据都会被删除

    ```sql
    create table t1
    (tid int,tname string,age int);
    新建表后添加新的列
    alter table t1 add columns(english int);
    删除表
    drop table t1;

    指明表的存放位置
    create table t2
    (tid int,tname string,age int)
    location '/mytable/hive/t2';

    指明列与列之间的分割符,默认是制表符
    create table t3
    (tid int,tname string,age int)
    row format delimited fields terminated by ',';

    使用子查询来创建表
    create table t4
    as
    select * from sample_data;
    ```

- Partition 分区表

  - Partition 对应于数据库的Partition列的密集索引

  - 在Hive中，表中的一个Partition对应于表下的一个目录，所有的Partition的数据都存储在对应的目录中

    ```sql
    创建以性别为分区的表
    create table t1
    (sid int,sname string)
    partitioned by (gender string)
    row format delimited fields terminated by ',';

    insert into table t1 partition(gender='M') select * from t2 where gender = 'M';

    生成t2的查询计划（t2未分区）对比分区带来的查询效率
    explain select * from t2 where gender='M';
    explain select * from t1 where gender='M';

    查看当前分区信息
    show partitions table_name;

    删除分区
    alter table tmp_h02_click_log_baitiao drop partition(dt='2014-03-01');
    ```

- External Table 外部表

  - 指向已经在HDFS中存在的数据，可以创建Partition

  - 它和内部表在元数据的组织上是相同的，而实际数据的存储则有较大的差异

  - 外部表**只有一个过程**，加载数据和创建表同时完成，并不会移动到数据仓库目录中，只是与外部数据建立一个链接，当删除一个外部表时，仅删除该链接

    ```sql
    假如在HDFS中的input目录（不在/user/hive）中存在student01.txt student02.txt

    创建外部表
    create external table t1
    (sid int,sname string,age int)
    row format delimited fields terminated by ','
    location '/input';
    ```

- Bucket Table 桶表

  - 桶表是对数据进行哈希取值，然后放到不同文件中存储

    ```sql
    create table t1
    (sid int,sname string,age int)
    clustered by(sname) into 5 buckets;
    ```

#### 视图

- 视图是一种**虚表**（在oracle和mysql中有一种物化虚表，可以存数据提高查询效率），是一个逻辑概念，可以跨越多张表

- 视图建立在已有表的基础上，视图赖以建立的这些表称为**基表**

- 视图可以简化复杂的查询

  ```sql
  create view empinfo
  as
  select e.empno,e.ename,e.sal
  from emp e,dept d
  where e.deptno=d.deptno;
  ```

### Hive数据的导入

#### 使用load语句

```sql
LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE]
INTO TABLE tablename [PARTITION (partcol1=val1,partcol2=val2 ...)]

//文件在Linux系统上，不在HDFS上
load data local inpath '/root/data/student01.txt' into table t2;
//将某个文件夹内所有数据都导入t2，并且覆盖原来数据
load data local inpath '/root/data/' overwrite into table t2;
//将HDFS文件导入Hive中
load data inpath '/input/student01.txt' into table t2;
//导入带有分区的表
load data inpath '/input/student01.txt' into table t2 partition (gender = 'M');
```

#### 使用Sqoop导入

- 什么是Sqoop？
  - 是Apache下开源的专门用来做批量数据的导入和导出