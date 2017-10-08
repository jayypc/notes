# Spring Boot 参考指南

## 概述

- 创建可以独立运行的Spring应用。
- 直接嵌入Tomcat或Jetty服务器，不需要部署WAR文件。
- 提供推荐的基础POM文件来简化Apache Maven配置。
- 尽可能的根据项目依赖来自动配置Spring框架。
- 提供可以直接在生产环境中使用的功能，如性能指标、应用信息和应用健康检查。
- 没有代码生成，也没有XML配置文件。

> 从最根本上来讲，Spring Boot就是一些库的集合，它能够被任意项目的构建系统所使用。简便起见，该框架也提供了命令行界面，它可以用来运行和测试Boot应用。框架的发布版本，包括集成的CLI（命令行界面），可以在Spring仓库中手动下载和安装。一种更为简便的方式是使用Groovy环境管理器（Groovy enVironment Manager，GVM），它会处理Boot版本的安装和管理。Boot及其CLI可以通过GVM的命令行gvm install springboot进行安装。在OS X上安装Boot可以使用Homebrew包管理器。为了完成安装，首先要使用brew tap pivotal/tap切换到Pivotal仓库中，然后执行brew install springboot命令。
> 要进行打包和分发的工程会依赖于像Maven或Gradle这样的构建系统。为了简化依赖图，Boot的功能是模块化的，通过导入Boot所谓的“starter”模块，可以将许多的依赖添加到工程之中。为了更容易地管理依赖版本和使用默认配置，框架提供了一个parent POM，工程可以继承它。

## 使用Spring Boot

### 构建系统

- 依赖管理
- Maven

### 组织代码

- 尽量不用default包
- main类

```java
package com.example.myproject;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {
  public static void main(String[] args) {
  	SpringApplication.run(Application.class, args);
  }
}

//只添加一个 @EnableAutoConfiguration 注解，通常建议将它添加到主配置类（ primary @Configuration ） 上

//@SpringBootApplication 注解等价于以默认属性使用 @Configuration ， @EnableAutoConfiguration 和 @ComponentScan 
```

### 配置类

- 不需要将所有的 @Configuration 放进一个单独的类， @Import 注解可以用来导入其他配置类。另外，你也可以使用 @ComponentScan 注解自动收集所有Spring组件，包括 @Configuration 类。
- 如果必须使用XML配置，建议你仍旧从一个 @Configuration 类开始，然后使用 @ImportResource 注解加载XML配置文件。

### 自动配置

- 查看自动配置：在运行应用时打开 --debug 开关，这将为核心日志开启debug日志级别，并将自动配置相关的日志输出
  到控制台。
- 禁用特定的自动配置项

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;
@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```

### Spring Beans和依赖注入

- 使用 @ComponentScan 注解搜索beans，并结合 @Autowired 构造器注入。

  使用方法： 将应用的main类放到包的最上层，即rootpackage ，那么你就可以添加 @ComponentScan 注解而不需要任何参数，所有应用组件（@Component , @Service , @Repository , @Controller 等） 都会自动注册成Spring Beans。

  ```java
  @Service
  public class DatabaseAccountService implements AccountService {
    private final RiskAssessor riskAssessor;
    
    @Autowired
    public DatabaseAccountService(RiskAssessor riskAssessor) {
    	this.riskAssessor = riskAssessor;
    } 
    
    // ...
  }
  ```

### 运行程序

- IDE运行

- 打包后运行

  - 打包

    ```xml
    <!--添加spring-boot-maven-plugin插件后运行mvn package打包
    一般的maven项目的打包命令，不会把依赖的jar包也打包进去的，只是会放在jar包的同目录下，能够引用就可以了，但是spring-boot-maven-plugin插件，会将依赖的jar包全部打包进去-->
    <build>
      <plugins>
        <plugin>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
      </plugins>
    </build>
    ```

  - 运行

    ```shell
    $ java -jar target/myproject-0.0.1-SNAPSHOT.jar
    $ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
    ```

- Maven插件运行

  ```shell
  #跟在IDE运行一样支持热加载
  $ mvn spring-boot:run

  #设定操作系统环境变量
  $ export MAVEN_OPTS=-Xmx1024m -XX:MaxPermSize=128M
  ```

### 开发者工具

- Spring Boot包含了一些额外的工具集，用于提升Spring Boot应用的开发体验。 spring-boot-devtools 模块可以included到任何模块中，以提供development-time特性，你只需简单的将该模块的依赖添加到构建中： 

  ```xml
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <optional>true</optional>
    </dependency>
  </dependencies>
  ```

  正式环境不需要spring-boot-devtools，则需要对这个依赖做修改

- 默认属性：spring-boot-devtools会自动应用development-time来修改某些支持的库的缓存设置（例将Thymeleaf 的spring.thymeleaf.cache的属性改为false）

- 自动重启：在IDE环境下，只要classpath下的文件有变动，它就会自动重启。如果用Maven或Gradle启动应用需要开启fork

  ```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
        	<fork>true</fork>
        </configuration>
      </plugin>
    </plugins>
  </build>
  ```

## Spring Boot特性

