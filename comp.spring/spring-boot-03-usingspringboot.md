## Using Spring Boot

本章节将会详细介绍如何使用Spring Boot。它覆盖了构建系统，自动配置和，如何运行应用等主题等主题。我们也覆盖了一些Spring Boot最佳实践。尽管Spring Boot没有什么特别的（只是一个你能消费的库），但仍有一些建议，如果你遵循的话将会让你的开发进程更容易。

如果你刚接触Spring Boot，那最好先读下上一章节的[Getting Started](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#getting-started)指南。

### 1. 系统构建
强烈建议你选择一个支持 [依赖管理](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-dependency-management)，能消费发布到"Maven中央仓库"的artifacts的构建系统。我们推荐你选择Maven或Gradle。选择其他构建系统来使用Spring Boot也是可能的（比如Ant），但它们不会被很好的支持。

#### 1.1 依赖管理

Spring Boot每次发布时都会提供一个它所支持的精选依赖列表。实际上，在构建配置里你不需要提供任何依赖的版本，因为Spring Boot已经替你管理好了。当更新Spring Boot时，那些依赖也会一起更新。

>如果有必要，你可以指定依赖的版本来覆盖Spring Boot默认版本。

精选列表包括所有能够跟Spring Boot一起使用的Spring模块及第三方库，该列表可以在[材料清单(spring-boot-dependencies)](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-maven-without-a-parent)获取到，也可以找到一些支持[Maven](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-maven-parent-pom)和[Gradle](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-gradle-dependency-management)的资料。

>Spring Boot每次发布都关联一个Spring框架的基础版本，所以强烈建议你不要自己指定Spring版本。

#### 1.2 Maven

Maven用户可以继承 `spring-boot-starter-parent` 项目来获取合适的默认设置。该parent项目提供以下特性：
* 默认编译级别为Java 1.6
* 源码编码为`UTF-8`
* 一个[Dependency management](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-dependency-management)节点，允许你省略常见依赖的`<version>`标签，继承自`spring-boot-dependencies` POM。
* 恰到好处的[资源过滤](https://maven.apache.org/plugins/maven-resources-plugin/examples/filter.html)
* 恰到好处的插件配置（[exec插件](http://www.mojohaus.org/exec-maven-plugin/)，[surefire](http://maven.apache.org/surefire/maven-surefire-plugin/)，[Git commit ID](https://github.com/ktoso/maven-git-commit-id-plugin)，[shade](http://maven.apache.org/plugins/maven-shade-plugin/)）
* 恰到好处的对 `application.properties` 和 `application.yml`进行筛选，包括特定profile（profile-specific）的文件，比如 `applicationfoo.properties` 和 `application-foo.yml`

由于配置文件默认接收Spring风格的占位符（`${...}`），所以Maven filtering需改用`@..@`占位符（你可以使用Maven属性`resource.delimiter`来覆
盖它）。

**1.2.1 继承`starter parent`**  

如果你想配置项目，让其继承自 `spring-boot-starter-parent`，只需将 parent 按如下设置：
```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.1.RELEASE</version>
</parent>
```
>只需在该依赖上指定Spring Boot版本，如果导入其他的starters，放心的省略版本号好了。

可以在自己的项目中通过覆盖属性来覆盖个别的依赖。例如，可以将以下设置添加到 `pom.xml` 中来升级Spring Data到另一个发布版本。
```xml
<properties>
  <spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>
```
>注: 查看[`spring-boot-dependencies` pom](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-dependencies/pom.xml)获取支持的属性列表。

**1.2.2 不使用 parent POM**  

不是每个人都喜欢继承 `spring-boot-starter-parent` POM，比如你可能需要 使用公司的标准parent，或只是倾向于显式声明所有的Maven配置。

如果你不想使用`spring-boot-starter-parent`，通过设置`scope=import`的依赖，你仍能获取到依赖管理的好处：
```xml
<dependencyManagement>
     <dependencies>
        <dependency>
            <!-- Import dependency management from Spring Boot -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.0.0.BUILD-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
以上设置不允许你使用属性覆盖个别依赖，为了达到这个目的，你需要在项目的`dependencyManagement`节点中，在 `spring-boot-dependencies` 实体前插
入一个节点。例如，为了将Spring Data升级到另一个发布版本，你需要将以下配置添加到 `pom.xml` 中：
```xml
<dependencyManagement>
    <dependencies>
        <!-- Override Spring Data release train provided by Spring Boot -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-releasetrain</artifactId>
            <version>Fowler-SR2</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.0.0.BUILD-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
>示例中，我们指定了一个BOM，但任何的依赖类型都可以通过这种方式覆盖。

**改变Java版本**

`spring-boot-starter-parent` 选择了相当保守的Java兼容策略，如果你遵循我们的建议，使用最新的Java版本，可以添加一个 `java.version` 属性：
```xml
<properties>
  <java.version>1.8</java.version>
</properties>
```

**1.2.3 使用Spring Boot Maven插件**

Spring Boot包含一个[Maven插件](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-maven-plugin)，它可以将项目打包成一个可执行jar。如果想使用它，你可以将该插件添加到 `<plugins>` 节点处：
```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```
>如果使用Spring Boot starter parent pom，你只需要添加该插件而无需配置它，除非你想改变定义在partent中的设置。

#### 1.3 Gradle

Gradle用户可以直接在它们的`dependencies`节点处导入”starter POMs“。跟Maven不同的是，这里没有用于导入共享配置的"超父"（super parent）。
```
repositories {
    maven { url "http://repo.spring.io/snapshot" }
    maven { url "http://repo.spring.io/milestone" }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web:2.0.0.BUILD-SNAPSHOT")
}
```

`spring-boot-gradle-plugin`插件也是可以使用的，它提供创建可执行jar和从source运行项目的任务。它也添加了一个ResolutionStrategy用于让你省略常用依赖的版本号：
```
buildscript {
    repositories {
        jcenter()
        maven { url 'http://repo.spring.io/snapshot' }
        maven { url 'http://repo.spring.io/milestone' }
    }
    dependencies {
        classpath 'org.springframework.boot:spring-boot-gradle-plugin:2.0.0.BUILD-SNAPSHOT'
    }
}

apply plugin: 'java'
apply plugin: 'org.springframework.boot'

repositories {
    jcenter()
    maven { url 'http://repo.spring.io/snapshot' }
    maven { url 'http://repo.spring.io/milestone' }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}
```

#### 1.4 Ant

略。


#### 1.5 Starters

Starter POMs是可以包含到应用中的一个方便的依赖关系描述符集合。你可以获取所有Spring及相关技术的一站式服务，而不需要翻阅示例代码，拷贝粘贴大量的依赖描述符。例如，如果你想使用Spring和JPA进行数据库访问，只需要在你的项目中包含`spring-boot-starter-data-jpa`依赖，然后你就可以开始了。

该starters包含很多你搭建项目，快速运行所需的依赖，并提供一致的，管理的传递依赖集。

>**名字有什么含义**：所有官方starters遵循相似的命名模式：	`spring-bootstarter-*`，在这里 `*` 是一种特殊的应用程序类型。该命名结构旨在帮你找到需 要的starter。很多集成于IDEs中的Maven插件允许你通过名称name搜索依赖。例 如，使用相应的Eclipse或STS插件，你可以简单地在POM编辑器中点击	`ctrl-space`，然后输入"spring-boot-starter"就可以获取一个完整列表。  

>正如[Creating your own starter](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-custom-starter)章节中讨论的，第三方starters不应该以	`spring-boot` 开头，因为它跟Spring Boot官方artifacts冲突。一个`acme`的第三方starter通常命名为	`acme-spring-boot-starter`。

下面的应用程序starters是Spring Boot在`org.springframework.boot`组下提供的([在线参考](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-starter))：

**表1.1. Spring Boot application starters**
* `spring-boot-starter:` 核心Spring Boot starter，包括自动配置支持，日志和YAML
* `spring-boot-starter-actuator:` 生产准备的特性，用于帮你监控和管理应用
* `spring-boot-starter-amqp:` 对"高级消息队列协议"的支持，通过spring-rabbit实现
* `spring-boot-starter-aop:` 对面向切面编程的支持，包括spring-aop和AspectJ
* `spring-boot-starter-batch:` 对Spring Batch的支持，包括HSQLDB数据库
* `spring-boot-starter-cloud-connectors:` 对Spring Cloud Connectors的支持，简化在云平台下（例如，Cloud Foundry 和Heroku）服务的连接
* `spring-boot-starter-data-elasticsearch:` 对Elasticsearch搜索和分析引擎的支持，包括spring-data-elasticsearch
* `spring-boot-starter-data-gemfire:` 对GemFire分布式数据存储的支持，包括spring-data-gemfire
* `spring-boot-starter-data-jpa:` 对"Java持久化API"的支持，包括spring-data-jpa，spring-orm和Hibernate
* `spring-boot-starter-data-mongodb:` 对MongoDB NOSQL数据库的支持，包括spring-data-mongodb
* `spring-boot-starter-data-rest:` 对通过REST暴露Spring Data仓库的支持，通过spring-data-rest-webmvc实现
* `spring-boot-starter-data-solr:` 对Apache Solr搜索平台的支持，包括spring-data-solr
* `spring-boot-starter-freemarker:`对FreeMarker模板引擎的支持
* `spring-boot-starter-groovy-templates:` 对Groovy模板引擎的支持
* `spring-boot-starter-hateoas:` 对基于HATEOAS的RESTful服务的支持，通过spring-hateoas实现
* `spring-boot-starter-hornetq:` 对"Java消息服务API"的支持，通过HornetQ实现
* `spring-boot-starter-integration:` 对普通spring-integration模块的支持
* `spring-boot-starter-jdbc:` 对JDBC数据库的支持
* `spring-boot-starter-jersey:` 对Jersey RESTful Web服务框架的支持
* `spring-boot-starter-jta-atomikos:` 对JTA分布式事务的支持，通过Atomikos实现
* `spring-boot-starter-jta-bitronix:` 对JTA分布式事务的支持，通过Bitronix实现
* `spring-boot-starter-mail:` 对javax.mail的支持
* `spring-boot-starter-mobile:` 对spring-mobile的支持
* `spring-boot-starter-mustache:` 对Mustache模板引擎的支持
* `spring-boot-starter-redis:` 对REDIS键值数据存储的支持，包括spring-redis
* `spring-boot-starter-security:` 对spring-security的支持
* `spring-boot-starter-social-facebook:` 对spring-social-facebook的支持
* `spring-boot-starter-social-linkedin:` 对spring-social-linkedin的支持
* `spring-boot-starter-social-twitter:` 对spring-social-twitter的支持
* `spring-boot-starter-test:` 对常用测试依赖的支持，包括JUnit, Hamcrest和Mockito，还有spring-test模块
* `spring-boot-starter-thymeleaf:` 对Thymeleaf模板引擎的支持，包括和Spring的集成
* `spring-boot-starter-velocity:` 对Velocity模板引擎的支持
* `spring-boot-starter-web:` 对全栈web开发的支持，包括Tomcat和spring-webmvc
* `spring-boot-starter-websocket:` 对WebSocket开发的支持
* `spring-boot-starter-ws:` 对Spring Web服务的支持

**表1.2. Spring Boot生产准备的starters**
* `spring-boot-starter-actuator`: 添加生产准备特性，比如指标和监控
* `spring-boot-starter-remote-shell`: 添加远程ssh shell支持

**表1.3. Spring Boot technical starters**
* `spring-boot-starter-jetty`: 导入Jetty HTTP引擎（作为Tomcat的替代）
* `spring-boot-starter-log4j`: 对Log4J日志系统的支持
* `spring-boot-starter-logging`: 导入Spring Boot的默认日志系统（Logback）
* `spring-boot-starter-tomcat`: 导入Spring Boot的默认HTTP引擎（Tomcat）
* `spring-boot-starter-undertow`: 导入Undertow HTTP引擎（作为Tomcat的替代）

>注：查看GitHub上位于`spring-boot-starters`模块内的[README文件](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-starters/README.adoc)，可以获取到一个社区贡献的其他starter POMs列表。

### 2. 组织代码

Spring Boot不要求使用任何特殊的代码结构，不过，遵循以下的一些最佳实践还是挺有帮助的。

#### 2.1 使用`default`包

当类没有声明 package 时，它被认为处于 `default package` 下。通常不推荐使用 `default package` ，因为对于使用 `@ComponentScan`，`@EntityScan`或 `@SpringBootApplication` 注解的Spring Boot应用来说，它会扫描每个jar中的类，这会造成一定的问题。

> 注: 我们建议你遵循Java推荐的包命名规范，使用一个反转的域名。

#### 2.2 放置应用的main类

通常建议将应用的main类放到其他类所在包的顶层(root package)，并将 @EnableAutoConfiguration 注解到你的main类上，这样就隐式地定义了一个基础的包搜索路径（search package），以搜索某些特定的注解实体（比如`@Service`，`@Component`等） 。例如，如果你正在编写一个JPA应用，Spring将搜索 `@EnableAutoConfiguration` 注解的类所在包下的 `@Entity` 实体。

采用`root package`方式，你就可以使用 `@ComponentScan` 注解而不需要指定 `basePackage` 属性，也可以使用 `@SpringBootApplication` 注解，只要将`main`类放到`root package`中。

下面是一个典型的结构：
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
`Application.java` 将声明 `main` 方法，还有基本的 `@Configuration`。
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


### 3. 配置类

Spring Boot提倡基于Java的配置。尽管你可以使用XML源调用 `SpringApplication.run()`，不过还是建议你使用 `@Configuration` 类作为
主要配置源。通常定义了 `main` 方法的类也是使用 `@Configuration` 注解的一个很好的替补。  
> 注：虽然网络上有很多使用XML配置的Spring示例，但你应该尽可能的使用基于Java的配置，搜索查看 `enable*` 注解就是一个好的开端。

#### 3.1 导入其他配置类

你不需要将所有的 `@Configuration` 放进一个单独的类，`@Import` 注解可以用来导入其他配置类。另外，你也可以使用 `@ComponentScan` 注解自动收集所有Spring组件，包括 `@Configuration` 类。

#### 3.2 导入XML配置

如果必须使用XML配置，建议你仍旧从一个 `@Configuration` 类开始，然后使用 `@ImportResource` 注解加载XML配置文件。


### 4. 自动配置

Spring Boot自动配置（auto-configuration）尝试根据添加的jar依赖自动配置你的Spring应用。例如，如果classpath下存在 `HSQLDB`，并且你没有手动配置任何数据库连接的beans，那么Spring Boot将自动配置一个内存型（in-memory）数据库。
实现自动配置有两种可选方式，分别是将 `@EnableAutoConfiguration` 或 `@SpringBootApplication` 注解到 `@Configuration` 类上。
> 注：你应该只添加一个 `@EnableAutoConfiguration` 注解，通常建议将它添加到主配置类（primary `@Configuration`）上。

#### 4.1 逐步替换自动配置

自动配置（Auto-configuration）是非侵入性的，任何时候你都可以定义自己的配置类来替换自动配置的特定部分。例如，如果你添加自己的 DataSource bean，默认的内嵌数据库支持将不被考虑。

如果需要查看当前应用启动了哪些自动配置项，你可以在运行应用时打开 `--debug` 开关，这将为核心日志开启debug日志级别，并将自动配置相关的日志输出到控制台。

#### 4.2 禁用特定的自动配置项

如果发现启用了不想要的自动配置项，你可以使用 `@EnableAutoConfiguration` 注解的`exclude`属性禁用它们：
```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```
如果该类不在classpath中，你可以使用该注解的 `excludeName`属 性，并指定全限定名来达到相同效果。最后，你可以通过`spring.autoconfigure.exclude`属性exclude多个自动配置项（一个自动配置项集合）。
> 通过注解级别或`exclude`属性都可以定义排除项。


### 5. Spring Beans和依赖注入
你可以自由地使用任何标准的Spring框架技术去定义beans和它们注入的依赖。简单起见，我们经常使用 `@ComponentScan` 注解搜索beans，并结
合 `@Autowired` 构造器注入。  

如果遵循以上的建议组织代码结构(将应用的`main`类放到包的最上层，即root package)，那么你就可以添加`@ComponentScan`注解而不需要任何参数，所有应
用组件（`@Component`,`@Service`,`@Repository`,`@Controller`等）都会自动注册成Spring Beans。

下面是一个 `@Service` Bean的示例，它使用构建器注入获取一个需要的	`RiskAssessor` bean。
```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

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
>注意使用构建器注入允许 `riskAssessor` 字段被标记为`final`，这意味着	`riskAssessor` 后续是不能改变的。


### 6. 使用@SpringBootApplication注解
`@SpringBootApplication` 注解等价于以默认属性使用 `@Configuration`，`@EnableAutoConfiguration` 和 `@ComponentScan`

很多Spring Boot开发者总是使用`@Configuration`，`@EnableAutoConfiguration`和`@ComponentScan`注解他们的main类。由于这些注解被如此频繁地一块使用（特别是你遵循以上[最佳实践](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-structuring-your-code)时），Spring Boot提供一个方便的`@SpringBootApplication`选择。

该`@SpringBootApplication`注解等价于以默认属性使用`@Configuration`，`@EnableAutoConfiguration`和`@ComponentScan`。

```java
package com.example.myproject;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```
>`@SpringBootApplication` 注解也提供了用于自定义	`@EnableAutoConfiguration` 和 `@ComponentScan`	属性的别名（aliases）。


### 7. 运行应用程序

将应用打包成jar，并使用内嵌HTTP服务器的一个最大好处是，你可以像其他方式那样运行你的应用程序。调试Spring Boot应用也很简单，你都不需要任何特殊IDE插件或扩展！

#### 7.1 从IDE中运行

你可以从IDE中运行Spring Boot应用，就像一个简单的Java应用，但是，你首先需要导入项目。导入步骤跟你的IDE和构建系统有关。大多数IDEs能够直接导入Maven项目，例如Eclipse用户可以选择`File`菜单的`Import…​` --> `Existing Maven Projects`。

如果不能直接将项目导入IDE，你可以需要使用构建系统生成IDE元数据。Maven有针对Eclipse和IDEA的插件；Gradle为各种IDEs提供插件。

>如果意外地运行一个web应用两次，你将看到一个"端口已在使用中"错误。为了确保任何存在的实例是关闭的，STS用户可以使用`Relaunch`按钮而不是`Run`按钮。



#### 7.2 作为一个打包后的应用运行

如果使用Spring Boot Maven或Gradle插件创建一个可执行jar，你可以使用 `java -jar` 运行应用。例如：  
`$ java -jar target/myproject-0.0.1-SNAPSHOT.jar`

Spring Boot支持以远程调试模式运行一个打包的应用，下面的命令可以为应用关联一个调试器：
```  
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
  -jar target/myproject-0.0.1-SNAPSHOT.jar
```

#### 7.3 使用Maven插件运行  
Spring Boot Maven插件包含一个 `run` 目标，可用来快速编译和运行应用程序，并且跟在IDE运行一样支持热加载。  
`$ mvn spring-boot:run`   

你可以使用一些有用的操作系统环境变量：  
`$ export MAVEN_OPTS=-Xmx1024m`

#### 7.4 使用Gradle插件运行

Spring Boot Gradle插件也包含一个run目标，它可以用来以暴露的方式运行你的应用程序。不管你什么时候导入`spring-boot-plugin`，`bootRun`任务总是被添加进去。  
`$ gradle bootRun`

你可能想使用那些有用的操作系统环境变量：  
`$ export JAVA_OPTS=-Xmx1024m`

#### 7.5 热交换

由于Spring Boot应用只是普通的Java应用，所以JVM热交换（hot-swapping）也能开箱即用。不过JVM热交换能替换的字节码有限制，想要更彻底的解决方案可以使用Spring Loaded项目或JRebel。`spring-boot-devtools` 模块也支持快速应用重启.

关于热交换可以参考[“How-to”相应章节](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#howto-hotswapping)。

### 8. 开发者工具

Spring Boot包含了一些额外的工具集，用于提升Spring Boot应用的开发体验。 `spring-boot-devtools` 模块可以`included`到任何模块中，以提供`development-time`特性，你只需简单的将该模块的依赖添加到构建中：  
Maven
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
  </dependency>
</dependencies>
```
Gradle
```
dependencies {
  compile("org.springframework.boot:spring-boot-devtools")
}
```
在运行一个完整的，打包过的应用时，开发者工具（devtools）会被自动禁用。

如果应用使用 `java -jar` 或特殊的类加载器启动，都会被认为是一个产品级的应用（production application），从而禁用开发者工具。为了防止devtools传递到项目中的其他模块，设置该依赖级别为`optional`是个不错的实践。不过Gradle不支持 optional 依赖，所以你可能要了解下 `propdeps-plugin`。如果想确保devtools绝对不会包含在一个产品级构建中，你可以使用 `excludeDevtools` 构建属性彻底移除该JAR，Maven和Gradle都支持该属性。

#### 8.1 默认属性
Spring Boot支持的一些库（libraries）使用缓存提高性能。虽然缓存在生产环境很有用，但开发期间就是个累赘了。如果在IDE里修改了模板，你可能会想立即看到结果。  
缓存选项通常配置在 `application.properties` 文件中，比如Thymeleaf提供了 `spring.thymeleaf.cache` 属性，`spring-boot-devtools` 模块会自动应用敏感的 `development-time` 配置，而不是手动设置这些属性。  
*注 查看`DevToolsPropertyDefaultsPostProcessor`获取完整的被应用的属性列表。*

#### 8.2 自动重启

如果应用使用 `spring-boot-devtools`，则只要`classpath`下的文件有变动，它就会自动重启。这在使用IDE时非常有用，因为可以很快得到代码改变的反馈。默认情况下，`classpath`下任何指向文件夹的实体都会被监控，*注意：一些资源的修改比如静态`assets`，视图模板不需要重启应用。*

触发重启 由于DevTools监控`classpath`下的资源，所以唯一触发重启的方式就是更新`classpath`。引起`classpath`更新的方式依赖于你使用的IDE，在Eclipse里，保存一个修改的文件将引起`classpath`更新，并触发重启。在IntelliJ IDEA中，构建工程（Build → Make Project）有同样效果。  

>你也可以通过支持的构建工具（比如，Maven和Gradle）启动应用，只要开启fork功能，因为DevTools需要一个隔离的应用类加载器执行正确的操作。*

自动重启跟`LiveReload`可以一起很好的工作。

DevTools依赖应用上下文的shutdown钩子来关闭处于重启过程的应用。  
Restart vs Reload Spring Boot提供的重启技术是通过使用两个类加载器实现的。  

如果发现重启对于你的应用来说不够快，或遇到类加载的问题，那你可以考虑reload技术，

**8.2.1 排除资源**

某些资源的变化没必要触发重启。默认情况下，位于`/resources`等路径下的资源变更不会触发重启，但会触发实时加载。你可以使用 `spring.devtools.restart.exclude` 属性自定义这些排除规则。

**8.2.2 查看其他路径**

如果想让应用在改变没有位于`classpath`下的文件时也会重启或重新加载，你可以使用 `spring.devtools.restart.additional-paths` 属性来配置监控变化的额外路径。

**8.2.3 禁用重启**

如果不想使用重启特性，你可以通过 `spring.devtools.restart.enabled` 属性来禁用它，通常情况下可以在 `application.properties` 文件中设置.

**8.2.4 使用触发器文件**

如果使用一个IDE连续不断地编译变化的文件，你可能倾向于只在特定时间触发重启，触发器文件可以帮你实现该功能。使用 `spring.devtools.restart.trigger-file` 属性可以指定触发器文件。

**8.2.5 自定义restart类加载器**

重启功能是通过两个类加载器实现的。对于大部分应用来说是没问题的，但有时候它可能导致类加载问题,需要自定义。

**8.2.6 已知限制**

重启功能不能跟使用标准 ObjectInputStream 反序列化的对象工作。可能需要使用Spring的 `ConfigurableObjectInputStream`。

#### 8.3 LiveReload
* `spring-boot-devtools` 模块包含一个内嵌的`LiveReload`服务器，它可以在资源改变时触发浏览器刷新。  
* `LiveReload`浏览器扩展可以免费从livereload.com站点获取，支持Chrome，Firefox，Safari等浏览器。  
* 如果不想在运行应用时启动`LiveReload`服务器，你可以将 `spring.devtools.livereload.enabled` 属性设置为`false`。  
* *每次只能运行一个`LiveReload`服务器*


#### 8.4 全局设置
在 $HOME 文件夹下添加一个 `.spring-boot-devtools.properties` 的文件可以用来配置全局的`devtools`设置

#### 8.5 远程应用
Spring Boot开发者工具并不仅限于本地开发，在运行远程应用时你也可以使用一些特性。远程支持是可选的，通过设置 `spring.devtools.remote.secret` 属性可以启用它。

`spring.devtools.remote.secret=mysecret`远程devtools支持分两部分：一个是接收连接的服务端端点，另一个是运行在IDE里的客户端应用。如果设置 spring.devtools.remote.secret 属性，服务端组件会自动启用，客户端组件必须手动启动。

>注意：运行远程应用有安全风险，生产环境慎用

**8.5.1 运行远程客户端应用**

远程客户端应用程序（remote client application）需要在IDE中运行，你需要使用跟将要连接的远程应用相同的`classpath`运行 `org.springframework.boot.devtools.RemoteSpringApplication`，传参为你要连接的远程应用URL。

强烈建议使用 `https://` 作为连接协议，这样传输通道是加密的，密码也不会被截获。  

如果需要使用代理连接远程应用，你需要配置 `spring.devtools.remote.proxy.host` 和 `spring.devtools.remote.proxy.port` 属性。

**8.5.2 远程更新**

远程客户端将监听应用的classpath变化，任何更新的资源都会发布到远程应用，并触发重启，这在你使用云服务迭代某个特性时非常有用。通常远程更新和重启比完整`rebuild`和`deploy`快多了。

**8.5.3 远程调试通道**

Java的远程调试在诊断远程应用问题时很有用，不幸的是，当应用部署在你的数据中心外时，它并不总能够启用远程调试。为了突破这些限制，devtools支持基于HTTP的远程调试通道。

### 9. 打包用于生产的应用

可执行jars可用于生产部署。由于它们是自包含的，非常适合基于云的部署。关于其他“生产准备”的特性，比如健康监控，审计和指标REST，或JMX端点，可以考虑
添加 `spring-boot-actuator`。具体参考Part V, “Spring Boot Actuator: Production-ready features”。

### 10. 接下来阅读什么

现在你应该明白怎么结合最佳实践使用Spring Boot，接下来可以深入学习特殊的部分 *Spring Boot features*，或者你可以跳过开头，阅读Spring Boot的 *production ready* 部分。
