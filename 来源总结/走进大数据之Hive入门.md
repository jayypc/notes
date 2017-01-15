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

