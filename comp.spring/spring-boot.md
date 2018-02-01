## Spring Boot介绍

Spring Boot简化了基于Spring的应用开发，你只需要"run"就能创建一个独立的，产品级别的Spring应用。 我们为Spring平台及第三方库提供开箱即用的设置，这样你就可以有条不紊地开始。多数Spring Boot应用只需要很少的Spring配置。  

你可以使用Spring Boot创建Java应用，并使用`java -jar`启动它或采用传统的war部署方式。我们也提供了一个运行"spring脚本"的命令行工具。   

我们主要的目标是：
* 为所有Spring开发提供一个从根本上更快，且随处可得的入门体验。
* 开箱即用，但通过不采用默认设置可以快速摆脱这种方式。
* 提供一系列大型项目常用的非功能性特征，比如：内嵌服务器，安全，指标，健康检测，外部化配置。
* 绝对没有代码生成，也不需要XML配置。


#### 系统构建

强烈建议你选择一个支持依赖管理，能消费发布到"Maven中央仓库"的artifacts的构建系统，比如`Maven`或`Gradle`。

**依赖管理**
* Spring Boot每次发布时都会提供一个它所支持的精选依赖列表。实际上，在构建配置里你不需要提供任何依赖的版本，因为Spring Boot已经替你管理好了。当更新Spring Boot时，那些依赖也会一起更新。  
* Spring Boot每次发布都关联一个Spring框架的基础版本，所以强烈建议你不要自己指定Spring版本。

**Maven**   
* Maven用户可以继承 `spring-boot-starter-parent` 项目来获取合适的默认设置。
* 该parent项目提供以下特性：  
  - 默认编译级别为Java 1.6
  - 源码编码为`UTF-8`
  - 一个`Dependency management`节点，允许你省略常见依赖的`<version>`标签。
  - 恰到好处的资源过滤
  - 恰到好处的插件配置（exec插件，surefire，Git commit ID，shade）
  - 恰到好处的对 `application.properties` 和 `application.yml`进行筛选，包括特定profile（profile-specific）的文件，比如 `applicationfoo.properties` 和 `application-foo.yml`  
  由于配置文件默认接收Spring风格的占位符（`${...}`），所以Maven filtering需改用`@..@`占位符（你可以使用Maven属性`resource.delimiter`来覆
盖它）。

Spring Boot依赖使用的`groupId`为`org.springframework.boot`。通常，你的Maven POM文件会继承`spring-boot-starter-parent`工程，并声明一个或多个“Starter POMs”依赖。此外，Spring Boot提供了一个可选的Maven插件，用于创建可执行jars。

`spring-boot-starter-parent`是使用Spring Boot的一种不错的方式，但它并不总是最合适的。有时你可能需要继承一个不同的父 POM，或只是不喜欢我们的默认配置，那你可以使用`import`作用域这种替代方案，具体查看 “Using Spring Boot without the parent POM”。

**典型的pom.xml**
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- 继承 spring-boot-starter-parent -->
    <!-- 只需在该依赖上指定 Spring Boot 版本 -->
    <parent>
        <!-- 应用默认从spring-boot-starter-parent继承-->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.1.RELEASE</version>
    </parent>

    <!-- 可以在自己的项目中通过覆盖属性来覆盖个别的依赖 -->
    <properties>
      <!-- 改变Java版本 -->
      <java.version>1.8</java.version>
      <!-- 升级Spring Data到另一个发布版本 -->
      <spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
    </properties>

    <!-- 依赖关系 -->
    <dependencies>
        <!-- 一或多个“Starter POMs”依赖: spring-boot-start-* -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
      </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <!-- Maven插件，可以将项目打包成一个可执行jar -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <!-- 添加 Spring仓库，spring-boot RELEASE版本不需要 -->
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
            <snapshots><enabled>true</enabled></snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
        </pluginRepository>
        <pluginRepository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```

#### 典型的代码结构
建议你遵循Java推荐的包命名规范，使用一个反转的域名。通常不推荐使用`default package`, 因为对于使用`@ComponentScan`,`@EntityScan`或`@SpringBootApplication` 注解的Spring Boot应用来说，它会扫描每个jar中的类，这会造成一定的问题。

通常建议将应用的`main`类放到其他类所在包的顶层(root package)，并将`@EnableAutoConfiguration`注解到你的`main`类上，这样就隐式地定义了一个基础的包搜索路径。

采用`root package`方式，你就可以使用`@ComponentScan`注解而不需要指定`basePackage`属性，也可以使用  `@SpringBootApplication`注解，只要将`main`类放到`root package`中。

```
com
+- example
    +- myproject
        +- Application.java
        |
        +- domain
        |   +- Customer.java
        |   +- CustomerRepository.java
        |
        +- service
        |   +- CustomerService.java
        |
        +- web
            +- CustomerController.java
```
`Application.java`将声明`main`方法，还有基本的`@Configuration`。
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
```

#### 配置

Spring Boot提倡基于Java的配置。尽管你可以使用XML源调用`SpringApplication.run()` ，不过还是建议你使用  `@Configuration`类作为主要配置源。

实现自动配置有两种可选方式，分别是将`@EnableAutoConfiguration`或`@SpringBootApplication`注解到`@Configuration`类上。

应该只添加一个`@EnableAutoConfiguration`注解，通常建议将它添加到主配置类（primary `@Configuration`）上。

如果发现启用了不想要的自动配置项，你可以使用  @EnableAutoConfiguration  注解的 exclude 属性禁用它们

`spring-boot-sample-xml`


#### 运行应用
* 从IDE中运行
  Eclipse支持直接导入Maven项目；
* 作为一个打包后的应用运行
  `$ java -jar target/myproject-0.0.1-SNAPSHOT.jar`  
  Spring Boot支持以远程调试模式运行一个打包的应用。
* 使用Maven插件运行
  `$ mvn spring-boot:run`
* 使用Gradle插件运行
  `$ gradle bootRun`
* 由于Spring Boot应用只是普通的Java应用，所以JVM热交换也能开箱即用。

#### 开发者工具
Spring Boot包含了一些额外的工具集，用于提升Spring Boot应用的开发体验。
* 默认属性
* 自动重启
* LiveReload
* 全局设置
  在`$HOME`文件夹下添加一个`.spring-boot-devtools.properties`的文件可以用来配置全局的`devtools`设置

#### 远程应用

**Java远程调试**

`java -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8004 -jar myapp.jar`  
或  
`java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8004 -jar myapp.jar`  
* `dt_socket`：使用的通信方式
* `server`：是主动连接调试器还是作为服务器等待调试器连接
* `suspend`：是否在启动JVM时就暂停，并等待调试器连接
* `address`：地址和端口，地址可以省略，两者用冒号分隔




调试端：  
`jdb -attach 192.168.99.23:8004`


#### spring.factories
spring-boot/resource/META-INF/spring.factories
```
# PropertySource Loaders
org.springframework.boot.env.PropertySourceLoader=\
org.springframework.boot.env.PropertiesPropertySourceLoader,\
org.springframework.boot.env.YamlPropertySourceLoader

# Run Listeners
org.springframework.boot.SpringApplicationRunListener=\
org.springframework.boot.context.event.EventPublishingRunListener

# Application Context Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
org.springframework.boot.context.ContextIdApplicationContextInitializer,\
org.springframework.boot.context.config.DelegatingApplicationContextInitializer,\
org.springframework.boot.context.embedded.ServerPortInfoApplicationContextInitializer

# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.ClearCachesApplicationListener,\
org.springframework.boot.builder.ParentContextCloserApplicationListener,\
org.springframework.boot.context.FileEncodingApplicationListener,\
org.springframework.boot.context.config.AnsiOutputApplicationListener,\
org.springframework.boot.context.config.ConfigFileApplicationListener,\
org.springframework.boot.context.config.DelegatingApplicationListener,\
org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener,\
org.springframework.boot.logging.ClasspathLoggingApplicationListener,\
org.springframework.boot.logging.LoggingApplicationListener

# Environment Post Processors
org.springframework.boot.env.EnvironmentPostProcessor=\
org.springframework.boot.cloud.CloudFoundryVcapEnvironmentPostProcessor,\
org.springframework.boot.env.SpringApplicationJsonEnvironmentPostProcessor

# Failure Analyzers
org.springframework.boot.diagnostics.FailureAnalyzer=\
org.springframework.boot.diagnostics.analyzer.BeanCurrentlyInCreationFailureAnalyzer,\
org.springframework.boot.diagnostics.analyzer.BeanNotOfRequiredTypeFailureAnalyzer,\
org.springframework.boot.diagnostics.analyzer.BindFailureAnalyzer,\
org.springframework.boot.diagnostics.analyzer.ConnectorStartFailureAnalyzer,\
org.springframework.boot.diagnostics.analyzer.NoUniqueBeanDefinitionFailureAnalyzer,\
org.springframework.boot.diagnostics.analyzer.PortInUseFailureAnalyzer,\
org.springframework.boot.diagnostics.analyzer.ValidationExceptionFailureAnalyzer

# FailureAnalysisReporters
org.springframework.boot.diagnostics.FailureAnalysisReporter=\
org.springframework.boot.diagnostics.LoggingFailureAnalysisReporter
```


#### Spring Boot中的注解
从Spring 4.x开始，Spring.io提供了三种方式编织Bean：
* 利用注解：隐式配置，例如：`@Autowired`、`@Bean`、`@Component`等，通过注解来简化xml文件。
* 利用Java文件：显示配置，比xml配置的优势是具备类型安全
* 利用传统的xml配置文件

**注解(annotations)列表**
* `@ResponseBody`
  用该注解修饰的函数，会将结果直接填充到HTTP的响应体中，一般用于构建RESTful的api；
* `@Controller`
  用于定义控制器类，在spring 项目中由控制器负责将用户发来的URL请求转发到对应的服务接口（service层）。
* `@RestController`
  `@ResponseBody`和`@Controller`的合集
* `@RequestMapping`
  提供路由信息，负责URL到Controller中的具体函数的映射。
* `@EnableAutoConfiguration`
  Spring Boot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将`@EnableAutoConfiguration`或者`@SpringBootApplication`注解添加到一个`@Configuration`类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用`@EnableAutoConfiguration`注解的排除属性来禁用它们。
* `@ComponentScan`
  表示将该类自动发现（扫描）并注册为Bean，可以自动收集所有的Spring组件，包括`@Configuration`类。我们经常使用`@ComponentScan`注解搜索beans，并结合`@Autowired`注解导入。
* `@Configuration`
  相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过`@Configuration`类作为项目的配置主类——可以使用`@ImportResource`注解加载xml配置文件。
* `@SpringBootApplication`
  相当于`@EnableAutoConfiguration`、`@ComponentScan`和`@Configuration`的合集。
* `@Import`
  用来导入其他配置类。
* `@ImportResource`
  用来加载xml配置文件。
* `@Autowired`
  自动导入依赖的bean
* `@Service`
  一般用于修饰service层的组件
* `@Repository`
  使用`@Repository`注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被`ComponetScan`发现并配置，同时也不需要为它们提供XML配置项。
