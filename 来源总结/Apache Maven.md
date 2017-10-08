# Apache Maven 入门篇

### Maven安装

- 解压

- 配置环境变量

- 配置maven的setting文件

  - 将本地仓库迁移至c盘

    ```xml
    <localRepository>D:\maven\repository</localRepository>
    ```

  - 设置国内仓库地址

    ```xml
    <mirrors>
    	<!-- 阿里云仓库 -->
    	<mirror>
    		<id>alimaven</id>
    		<mirrorOf>central</mirrorOf>
    		<name>aliyun maven</name>
    		<url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
    	</mirror>

    	<!-- 中央仓库1 -->
    	<mirror>
    		<id>repo1</id>
    		<mirrorOf>central</mirrorOf>
    		<name>Human Readable Name for this Mirror.</name>
    		<url>http://repo1.maven.org/maven2/</url>
    	</mirror>

    	<!-- 中央仓库2 -->
    	<mirror>
    		<id>repo2</id>
    		<mirrorOf>central</mirrorOf>
    		<name>Human Readable Name for this Mirror.</name>
    		<url>http://repo2.maven.org/maven2/</url>
    	</mirror>
    </mirrors>
    ```

### Maven名词解释

- modelVersion：POM 模型版本 4.0.0 固定
- groupId：一般指某个公司或者某个组织的某个项目 比如 org.springframework
- artifactId:一般指某个具体项目的某个具体模块 比如 spring-context
- Version：项目的版本 

### Maven常用命令

```
mvn compile 编译
mvn clean 清空
mvn test 测试
mvn package 打包(-e详细异常，-U强制更新)
mvn install 把项目安装到本地仓库,可以被本地工程引用(mvn clean install)
```

### Maven远程仓库搜索网页

​	http://mvnrepository.com/ 

### Maven Hello World实现

- 命令行进目录${basedir}
- 运行`mvn archetype:generate -DgroupId=com.mycompany.helloworld -DartifactId=helloworld -Dpackage=com.mycompany.helloworld -Dversion=1.0-SNAPSHOT`建立项目
- 默认回车选择
  - 项目模型（默认为maven-archetype-quickstart）
  - 模型版本（maven-archetype-quickstart版本选择，默认为1.1）
- 创建成功

> 目录说明

| **目录**                        | **目的**               |
| ----------------------------- | -------------------- |
| ${basedir}                    | 存放 pom.xml和所有的子目录    |
| ${basedir}/src/main/java      | 项目的 java源代码          |
| ${basedir}/src/main/resources | 项目的资源，比如说 property文件 |
| ${basedir}/src/test/java      | 项目的测试类，比如说 JUnit代码   |
| ${basedir}/src/test/resources | 测试使用的资源              |

- 进入工程helloword，运行`mvn package`构建项目
- 运行项目`java -cp target/helloworld-1.0-SNAPSHOT.jar com.mycompany.helloworld.App`
- 结果显示：Hello World!

### Maven eclipse插件

- 由于最新版的 Eclipse 代号 Mars，已经直接集成了 Maven，所以无需在安装 m2eclipse 插件 
- 配置
  - 配置插件设置文件：Preferences→Maven→User Setting→Global Settings
  - 配置下载jar源码和javadoc：Preferences→Maven 勾选Download Artifact Sources和Download Artifact JavaDoc



### 核心概念

#### POM（project object model）

> 一个项目所有的配置都放置在 POM 文件中：定义项目的类型、名字，管理依赖关系，定制插件的行为等等。比如说，你可以配置 compiler 插件让它使用 java 1.5 来编译。

- groupId, artifactId, packaging, version 叫作 maven 坐标，它能唯一的确定一个项目

  ```xml
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.mycompany.helloworld</groupId>
    <artifactId>helloworld</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>helloworld</name>
    <url>http://maven.apache.org</url>

    <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>3.8.1</version>
        <scope>test</scope>  
        <!-- optional 当该项目本身作为其他项目的一个依赖时标记该依赖为可选项。假设现在projectA有一个依赖性projectB，我们把projectB这个依赖项设为optional，这表示projectB在projectA的运行时不一定会用到。这个时候如果我们有另一个项目projectC，它依赖于projectA，那么这个时候因为projectB对于projectA是可选的，所以Maven在建立projectC的时候就不会安装projectB，这个时候如果projectC确实需要使用到projectB，那么它就可以定义自己对projectB的依赖。当一个依赖是可选的时候，我们把optional元素的值设为true，否则就不设置optional元素。 -->
        <!-- exclusions 考虑这样一种情况，我们的projectA依赖于projectB，然后projectB又依赖于projectC，但是在projectA里面我们不需要projectB依赖的projectC，那么这个时候我们就可以在依赖projectB的时候使用exclusions元素下面的exclusion排除projectC。-->
      </dependency>
    </dependencies>
  </project>
  ```

- 依赖特性

  - 依赖传递
    1. A → B → C → X(P1)	A → D → X(P2)	最短路径原则
     2. A → B → X(P1)A → D → X(P2)最先声明原则
  - 依赖范围
    - classpath(运行引入包路径)有三种：编译classpath、测试classpath、运行classpath
    - 根据上面的分类存在以下几种scope
      - compile：编译依赖范围，默认值，编译测试运行都有效
      - test：测试依赖范围，仅测试有效，比如JUnit
      - provided：已提供依赖范围，编译测试有效，运行无效，比如servlet-api、jsp-api（tomcat提供）
      - runtime：运行依赖范围，运行时有效，比如jdbc驱动实现
      - system：系统依赖范围，和provided类似，配合systemPath使用，指定依赖文件路径（了解）
      - import：导入依赖范围，使用dependencyManagement时，可以导入依赖配置（了解）

- 聚合和继承

  - 大项目一般会分成几个子项目。在这种情况下，每个子项目就会有自己的 POM 文件，然后它们会有一个共同的父项目。这样只要构建父项目就能够构建所有的子项目了。子项目的 POM 会继承父项目的 POM。

    > 示例（聚合和继承同时存在，且工程相互独立，目录结构不是父子关系）:

  - 另外，所有的 POM都继承了一个 Super-POM。Super-POM 设置了一些默认值，比如默认的目录结构，默认的插件等等，它遵循了惯例优于配置的原则。 如果想看到运行时候的 POM 的全部内容的话，可以运行下面的命令：

    `mvn help:effective-pom`

    > 子pom文件不是一旦继承了父pom就会无条件地继承它所有的依赖关系，即插件、类库等。如果子pom想继承父pom的某个插件，只需要引入父pom中该插件的groupId与artifactId信息（不用写该插件其它的配置信息）即可。这样子pom是可以有选择性的继承它自己所需要的东西。

  - 聚合

    - 新建一个Maven工程，选择Packaging方式为pom（还有jar、war两个选项）

    - 整个工程有用的只有pom.xml

    - 对pom.xml进行修改

    ```xml
    <modules>
    	<module>../user-dao</module>
      	<module>../user-service</module>
    </modules>
    ```

    - 这样就可以对这些模块进行统一管理

    - 对pom.xml右键选择Run as ：mvn clean则会对所有模块进行clean

  - 继承

    - 对多个模块中的pom文件引用相同的依赖进行统一关联，增加一个父类pom

    ```xml
    <!--在子类pom中引入父类pom-->
    <parent>  
           <groupId>com.java1234.user</groupId>  
           <artifactId>user-parent</artifactId>  
           <version>1.0-SNAPSHOT</version>  
           <relativePath>../user-parent/pom.xml</relativePath>  <!--寻找父类的路径 -->
    </parent>
    ```

    - 只是对版本号进行继承

    - 可以对一个模块的版本进行抽取，使用properties,dependencyManagement属性，使用${}引用（比如spring由很多模块组成）

    ```xml
    <properties>
    	<spring.version>4.1.7.RELEASE</spring.version>
    </properties>

    <dependencyManagement>
    	<dependencies>
    		<dependency>
    			<groupId>org.springframework</groupId>
    			<artifactId>spring-core</artifactId>
    			<version>${spring.version}</version>
    		</dependency>
    		<dependency>
    			<groupId>org.springframework</groupId>
    			<artifactId>spring-beans</artifactId>
    			<version>${spring.version}</version>
    		</dependency>
    		<dependency>
    			<groupId>org.springframework</groupId>
    			<artifactId>spring-tx</artifactId>
    			<version>${spring.version}</version>
    		</dependency>
    	</dependencies>
    </dependencyManagement>
    ```

  - 打包时候的java版本设定

    - cli的时候要编译的版本要与运行的jre一致

      - 编译的时候可以通过以下配置忽略未经检查或不安全的操作

      ```xml
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
            <testSource>1.8</testSource>
            <testTarget>1.8</testTarget>
                      <compilerArgument>-Xlint:unchecked</compilerArgument>
          </configuration>
        </plugin>
      </plugins>
      ```

    - eclipse的run Config里面配置运行的jre与要编译的一致

    - 通过配置一个自定义java.version来对应到spring-boot-maven-plugin中的引用

    ```xml
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>  
        <maven.compiler.target>1.8</maven.compiler.target>  
    </properties>
    ```

  - build中的配置

    - 两种类型的build:一种是project的子节点,另一种是profile的子节点
      - project下 的build比另一个多包含了两个特殊元素:<Directory> 和 <extensions>

    ```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"  
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">  
      ...  
      <!-- "Project Build" contains elements of the BaseBuild set and the Build set-->  
      <build>...</build>  
       
      <profiles>  
        <profile>  
          <!-- "Profile Build" contains elements of the BaseBuild set only -->  
          <build>...</build>  
        </profile>  
      </profiles>  
    </project>  
    ```

    - build下常用resources和plugins

      - resources主要用于打包资源文件，默认情况下maven只打包src/main/resources下的资源

      ```xml
      <build>  
          <finalName>test</finalName>  
          <!--  这样也可以把所有的xml文件，打包到相应位置。
        	其中**/*这样的写法，是为了保证各级子目录下的资源文件被打包-->
          <resources>  
              <resource>  
                  <directory>src/main/resources</directory>  
                  <includes>  
                      <include>**/*.properties</include>  
                      <include>**/*.xml</include>  
                      <include>**/*.tld</include>  
                  </includes>  
                  <filtering>false</filtering>  
              </resource>  
              <resource>  
                  <directory>src/main/java</directory>  
                  <includes>  
                      <include>**/*.properties</include>  
                      <include>**/*.xml</include>  
                      <include>**/*.tld</include>  
                  </includes>  
                  <filtering>false</filtering>  
              </resource>  
          </resources>  
      </build>  
      ```

#### Maven 插件

- 用`mvn archetype:generate` 命令来生成一个项目。这里的archetype 是一个插件的名字，generate是目标(goal)的名字。这个命令的意思是告诉 maven 执行 archetype 插件的 generate 目标。插件目标通常会写成 pluginId:goalId
- 一个目标是一个工作单元，而**插件则是一个或者多个目标的集合**。比如说Jar插件，Compiler插件，Surefire插件等。从看名字就能知道，Jar 插件包含建立Jar文件的目标， Compiler 插件包含编译源代码和单元测试代码的目标。Surefire 插件的话，则是运行单元测试。
- mvn 本身不会做太多的事情，它不知道怎么样编译或者怎么样打包。它把构建的任务交给插件去做。插件定义了常用的构建逻辑，能够被重复利用。这样做的好处是，一旦插件有了更新，那么所有的 maven 用户都能得到更新。

#### Maven 生命周期

- 用`mvc package`来构建项目，这里的package是一个maven的生命周期阶段 (lifecycle phase )。生命周期指项目的构建过程，它包含了一系列的有序的阶段 (phase)，而一个阶段就是构建过程中的一个步骤。

- 插件目标可以绑定到生命周期阶段上。**一个生命周期阶段可以绑定多个插件目标**。当 maven 在构建过程中逐步的通过每个阶段时，会执行该阶段所有的插件目标。

- maven 能支持不同的生命周期，但是最常用的是默认的Maven生命周期 (default Maven lifecycle )。如果你没有对它进行任何的插件配置或者定制的话，那么上面的命令 mvn package 会依次执行默认生命周期中直到包括 package 阶段前的所有阶段的插件目标：

  ```
  process-resources 阶段：resources:resources
  compile 阶段：compiler:compile
  process-classes 阶段：(默认无目标)
  process-test-resources 阶段：resources:testResources
  test-compile 阶段：compiler:testCompile
  test 阶段：surefire:test
  prepare-package 阶段：(默认无目标)
  package 阶段：jar:jar
  ```

