## Spring Boot Getting Started

如果你想从大体上了解Spring Boot或Spring，本章节正是你所需要的！本节中，我们会回答基本的"what?"，"how?"和"why?"等问题，并通过一些安装指南简单介绍
下Spring Boot。然后我们会构建第一个Spring Boot应用，并讨论一些需要遵循的核心原则。

### 1. Spring Boot介绍

Spring Boot简化了基于Spring的应用开发，你只需要"run"就能创建一个独立的，产品级别的Spring应用。 我们为Spring平台及第三方库提供开箱即用的设置，这样你就可以有条不紊地开始。多数Spring Boot应用只需要很少的Spring配置。  

你可以使用Spring Boot创建Java应用，并使用 `java -jar` 启动它或采用传统的war部署方式。我们也提供了一个运行"spring scripts"的命令行工具。  

我们主要的目标是：
* 为所有Spring开发提供一个从根本上更快，且随处可得的入门体验。
* 开箱即用，但通过不采用默认设置可以快速摆脱这种方式。
* 提供一系列大型项目常用的非功能性特征，比如：内嵌服务器，安全，指标，健康检测，外部化配置。
* 绝对没有代码生成，也不需要XML配置。

### 2. 系统要求

默认情况下，Spring Boot 2.0.0.BUILD-SNAPSHOT 需要 ***Java 8*** 和 ***Spring框架5.0.0.BUILD-SNAPSHOT或以上版本***。  
明确提供构建支持的有 ***Maven（3.2+）*** 和 ***Gradle（2.9以上 或 3）***。

**Servlet 容器**：开箱即用的内嵌容器包括 Tomcat 8.5 或 Jetty 9.4, Servlet版本 3.1。  
*也可以将Spring Boot应用部署到任何兼容Servlet 3.0+的容器。*

### 3. Spring Boot 安装

Spring Boot可以跟经典的Java开发工具（Eclipse，IntelliJ等）一起使用或安装成一个命令行工具。  
安装前检查Java版本: `$java -version`  
如果你是一个Java新手，或只是想体验一下Spring Boot，你可能想先尝试`Spring Boot CLI`，否则继续阅读“经典”地安装指南。

#### 3.1 为Java开发者准备的安装指南

对于java开发者来说，使用Spring Boot就跟使用其他Java库一样，只需要在你的classpath下引入适当的`spring-boot-*.jar`文件。Spring Boot不需要集成任何特殊的工具，所以你可以使用任何IDE或文本编辑器；同时，Spring Boot应用也没有什么特殊之处，你可以像对待其他Java程序那样运行，调试它。  

尽管可以拷贝Spring Boot jars，但我们还是建议你使用支持依赖管理的构建工具，比如Maven或Gradle。

**3.1.1. Maven安装**

Spring Boot兼容Apache Maven 3.2或更高版本。如果本地没有安装Maven，你可以参考[maven.apache.org](http://maven.apache.org/)上的指南。

>在很多操作系统上，可以通过包管理器来安装Maven。OSX Homebrew用户可以尝试 `brew install maven`，Ubuntu用户可以运行 `sudo apt-get install maven`。

Spring Boot依赖使用的`groupId`为`org.springframework.boot`。通常，你的Maven POM文件会继承`spring-boot-starter-parent`工程，并声明一个或多个 “[Starters](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-starter)” 依赖。此外，Spring Boot提供了一个可选的[Maven插件](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-maven-plugin)，用于创建可执行jars。

下面是一个典型的pom.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.0.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <!-- Add Spring repositories -->
    <!-- (you don't need this if you are using a .RELEASE version) -->
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
>`spring-boot-starter-parent`是使用Spring Boot的一种不错的方式，但它并不总是最合适的。有时你可能需要继承一个不同的父 POM，或只是不喜欢我们的默认配置，那你可以使用`import`作用域这种替代方案，具体查看 “[Using Spring Boot without the parent POM](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-maven-without-a-parent)”。


**3.1.2. Gradle**

Spring Boot兼容Gradle 2(2.9或更高版本)和Gradle 3。如果本地没有安装Gradle，你可以参考[www.gradle.org](http://www.gradle.org/)上的指南。

Spring Boot的依赖可通过`org.springframework.boot` `group`来声明。通常，你的项目将声明一个或多个 “[Starter](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-starter)” 依赖。Spring Boot提供了一个很有用的[Gradle插件](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-gradle-plugin)，可以用来简化依赖声明，创建可执行jars。

>当你需要构建项目时，Gradle Wrapper提供一种给力的获取Gradle的方式。它是一小段脚本和库，跟你的代码一块提交，用于启动构建进程，具体参考 [Gradle Wrapper](https://docs.gradle.org/2.14.1/userguide/gradle_wrapper.html)。

下面是一个典型的 `build.gradle` 文件：
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

jar {
    baseName = 'myproject'
    version =  '0.0.1-SNAPSHOT'
}

repositories {
    jcenter()
    maven { url "http://repo.spring.io/snapshot" }
    maven { url "http://repo.spring.io/milestone" }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}
```

#### 3.2 Spring Boot CLI安装

Spring Boot CLI是一个命令行工具，可用于快速搭建基于Spring的原型。它支持运行Groovy脚本，这也就意味着你可以使用类似Java的语法，但不用写很多的模板代码。

Spring Boot不一定非要配合CLI使用，但它绝对是Spring应用取得进展的最快方式。

Spring Boot CLI安装详细内容参见[文档](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#getting-started-installing-the-cli)， 包括以下几部分：
* 手动安装
* 使用SDKMAN安装
* 使用OSX Homebrew进行安装
* 使用MacPorts进行安装
* 命令行实现
* Spring CLI示例快速入门

#### 3.3 版本升级

如果你正在升级Spring Boot的早期发布版本，那最好查看下 [project wiki](https://github.com/spring-projects/spring-boot/wiki) 上的"release notes"，你会发现每次发布对应的升级指南和一个"new and noteworthy"特性列表。

想要升级一个已安装的CLI，你需要使用合适的包管理命令，例如 `brew upgrade`；如果是手动安装CLI，按照 [standard instructions](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#getting-started-manual-cli-installation) 操作并记得更新你的 `PATH` 环境变量以移除任何老的引用。

### 4. 开发第一个Spring Boot应用

我们将使用Java开发一个简单的"Hello World" web应用，以此强调下Spring Boot的一些关键特性。项目采用Maven进行构建，因为大多数IDEs都支持它。

>[spring.io](http://spring.io/)网站包含很多Spring Boot "入门" 指南，如果你正在找特定问题的解决方案，可以先去那瞅瞅。  
你也可以简化下面的步骤，直接从 [start.spring.io](https://start.spring.io/)  的依赖搜索器选中 `web` starter，这会自动生成一个新的项目结构，然后你就可以happy的敲[代码](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#getting-started-first-application-code)了。具体详情参考[文档](https://github.com/spring-io/initializr)。

在开始前，你需要打开终端检查下安装的Java和Maven版本是否可用：  
`$ java -version`  
`$ mvn -v`  

>该示例需要创建单独的文件夹，后续的操作建立在你已创建一个合适的文件夹，并且它是你的“当前目录”。

#### 4.1 创建POM

让我们以创建一个Maven pom.xml 文件作为开始吧，因为 pom.xml 是构建项目的处方！打开你最喜欢的文本编辑器，并添加以下内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.0.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Additional lines to be added here... -->

    <!-- (you don't need this if you are using a .RELEASE version) -->
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

这样一个可工作的构建就完成了，你可以通过运行 `mvn package` 测试它（暂时忽略"jar将是空的-没有包含任何内容！"的警告）。

>此刻，你可以将该项目导入到IDE中（大多数现代的Java IDE都包含对Maven的内建支持）。简单起见，我们将继续使用普通的文本编辑器完成该示例。


#### 4.2 添加classpath依赖

Spring Boot提供很多"Starters"，用来简化添加jars到 classpath 的操作。示例程序中已经在POM的 `partent` 节点使用了 `spring-boot-starter-parent`，它是一个特殊的starter，提供了有用的Maven默认设置。同时，它也提供一个`dependencymanagement`节点，这样对于期望（”blessed“）的依赖就可以省略version标记了。

其他”Starters“只简单提供开发特定类型应用所需的依赖。由于正在开发web应用，我们将添加 `spring-boot-starter-web` 依赖-但在此之前，让我们先看下目前的依赖：
```
$ mvn dependency:tree
[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```

`mvn dependency:tree`命令可以将项目依赖以树形方式展现出来。可以看到 spring-boot-starter-parent 本身并没有提供依赖。编辑 `pom.xml`，并
在 `parent` 节点下添加 `spring-boot-starter-web` 依赖：

``` xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>
```
如果再次运行 `mvn dependency:tree`，你将看到现在多了一些其他依赖，包括Tomcat web服务器和Spring Boot自身。


#### 4.3 编写代码

为了完成应用程序，我们需要创建一个单独的Java文件。Maven默认会编译 `src/main/java` 下的源码，所以你需要创建那样的文件结构，并添加一个名为 `src/main/java/Example.java` 的文件：
``` java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.stereotype.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Example.class, args);
    }

}
```
尽管代码不多，但已经发生了很多事情，让我们分步探讨重要的部分吧！

**4.3.1 @RestController 和 @RequestMapping 注解**

Example类上使用的第一个注解是 `@RestController`，这被称为构造型（stereotype）注解。它为阅读代码的人提供暗示（这是一个支持REST的控制器），对于Spring，该类扮演了一个特殊角色。在本示例中，我们的类是一个web `@Controller` ，所以当web请求进来时，Spring会考虑是否使用它来处理。

`@RequestMapping` 注解提供路由信息，它告诉Spring任何来自"/"路径的HTTP请 求都应该被映射到`home`方法。`@RestController` 注解告诉Spring以字符串的形式渲染结果，并直接返回给调用者。  

>`@RestController` 和`@RequestMapping`是Spring MVC中的注解（它们不是Spring Boot的特定部分）,具体参考Spring文档的[MVC章节](http://docs.spring.io/spring/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle#mvc)。

**4.3.2 @EnableAutoConfiguration 注解**

`@EnableAutoConfiguration` 注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。
由于 `spring-boot-starter-web` 添加了`Tomcat`和`Spring MVC`，所以`auto-configuration`将假定你正在开发一个web应用，并对Spring进行相应地设置。

>**Starters和Auto-Configuration**：Auto-configuration设计成可以跟"Starters"一起很好的使用，但这两个概念没有直接的联系。你可以自由地挑选starters以外的jar依赖，Spring Boot仍会尽最大努力去自动配置你的应用。

**4.3.3 main方法**

应用程序的最后部分是`main`方法，这是一个标准的方法，它遵循Java对于一个应用程序入口点的约定。我们的`main`方法通过调用`run`，将业务委托给了Spring Boot的`SpringApplication`类。`SpringApplication`将引导我们的应用，启动Spring，相应地启动被自动配置的Tomcat web服务器。我们需要将 `Example.class` 作为参数传递给`run`方法，以此告诉`SpringApplication`谁是主要的Spring组件，并传递`args`数组以暴露所有的命令行参数。

#### 4.4 运行示例

到此，示例应用可以工作了。由于使用了 `spring-boot-starter-parent` POM，这样我们就有了一个非常有用的run目标来启动程序。  
  `$ mvn spring-boot:run`  
如果使用浏览器打开`localhost:8080`，你应该可以看到如下输出：  
  `Hello World!`  
点击 `ctrl-c` 温雅地关闭应用程序。

#### 4.5 创建可执行jar

让我们通过创建一个完全自包含，并可以在生产环境运行的可执行jar来结束示例吧！可执行jars（有时被称为胖jars "fat jars"）是包含编译后的类及代码运行所需依赖jar的存档。

>**可执行jars和Java**  
>Java没有提供任何标准方式，用于加载内嵌jar文件（即jar文件中还包含jar文件），这对分发自包含应用来说是个问题。  
>为了解决该问题，很多开发者采用"共享的"jars。共享的jar只是简单地将所有jars的类打包进一个单独的存档，这种方式存在的问题是，很难区分应用程序中使用了哪些库。在多个jars中如果存在相同的文件名（但内容不一样）也会是一个问题。  
>Spring Boot采取一个[不同的方式](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#executable-jar)，允许你真正的直接内嵌jars。

为了创建可执行的jar，我们需要将`spring-boot-maven-plugin`添加到`pom.xml`中，在`dependencies`节点后面插入以下内容：
``` xml
<build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
</build>
```
>`spring-boot-starter-parent` POM包含绑定到repackage目标的 `<executions>` 配置。如果不使用parent POM，你需要自己声明该配置，具体
参考[插件文档](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/maven-plugin/usage.html)。

保存 pom.xml ，并从命令行运行 `mvn package`。

如果查看`target`目录，你应该可以看到 `myproject-0.0.1-SNAPSHOT.jar`，该文件大概有10Mb。想查看内部结构，可以运行 `jar tvf`：  
`$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar`  
在该目录下，你应该还能看到一个很小的名为 `myproject-0.0.1-SNAPSHOT.jar.original` 的文件，这是在Spring Boot重新打包前，Maven创建的原始jar文件。  

可以使用 `java -jar` 命令运行该应用程序：  
`$ java -jar target/myproject-0.0.1-SNAPSHOT.jar`  
如上所述，点击 `ctrl-c` 可以温雅地退出应用。


### 5. 接下来阅读什么

希望本章节已为你提供一些Spring Boot的基础部分，并帮你找到开发自己应用的方式。如果你是任务驱动型的开发者，那可以直接跳到 [spring.io](http://spring.io/)，check out一些[入门指南](http://spring.io/guides/)，以解决特定的 *"使用Spring如何做"* 的问题；我们也有Spring Boot相关的 [How-to](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#howto) 参考文档。

[Spring Boot仓库](http://github.com/spring-projects/spring-boot)有大量可以运行的[示例](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples)，这些示例代码是彼此独立的(运行或使用示例的时候不需要构建其他示例)。

否则，下一步就是阅读 [III. 使用Spring Boot](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot)，如果没耐心，可以跳过该章节，直接阅读 [IV. Spring Boot特性](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features)。
