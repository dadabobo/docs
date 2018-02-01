## Spring Boot CLI

Spring Boot CLI是一个命令行工具，如果想使用Spring进行快速开发可以使用它。它允许你运行Groovy脚本，这意味着你可以使用熟悉的类Java语法，并且没有那么多的模板代码。你可以通过Spring Boot CLI启动新项目，或为它编写命令。

### 1. 安装CLI

你可以手动安装Spring Boot CLI，也可以使用SDKMAN!（SDK管理器）或 Homebrew，MacPorts（如果你是一个OSX用户），具体安装指令参考 "Getting started" 的 “[Installing the Spring Boot CLI](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#getting-started-installing-the-cli)” 章节。

### 2. 使用CLI

一旦安装好CLI，你可以输入 `spring` 来运行它。如果不使用任何参数运行 `spring` ，将会展现一个简单的帮助界面.  
你可以使用 help 获取任何支持命令的详细信息.  
[本章详细内容](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#cli-using-the-cli) 包括：
* 使用CLI运行应用
* 测试你的代码
* 多源文件应用
* 应用打包
* 初始化新工程
* 使用内嵌shell
* 为CLI添加扩展

### 3. 使用Groovy beans DSL开发应用

Spring框架4.0版本对 `beans{}` "DSL"（借鉴自Grails）提供原生支持，你可以使用相同格式在Groovy应用程序脚本中嵌入bean定义。有时这是引入外部特性的很好方式，比如中间件声明。
```java
@Configuration
class Application implements CommandLineRunner {

    @Autowired
    SharedService service

    @Override
    void run(String... args) {
        println service.message
    }

}

import my.company.SharedService;

beans {
    service(SharedService) {
        message = "Hello World"
    }
}
```

你可以使用 `beans{}` 混合位于相同文件的类声明，只要它们都处于顶级，或如果喜欢的话，你可以将beans DSL放到一个单独的文件中。

### 4. 使用settings.xml配置CLI

Spring Boot CLI使用Maven的依赖解析引擎Aether来解析依赖，它充分利用发现的 `~/.m2/settings.xml` Maven设置去配置Aether。

CLI支持以下配置：
* Offline
* Mirrors
* Servers
* Proxies
* Profiles
  - Activation
  - Repositories
* Active profiles
更多信息可参考[Maven设置文档](https://maven.apache.org/settings.html)。

### 5. 接下来阅读什么

GitHub仓库有一些[groovy脚本示例](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-cli/samples)可用于尝试Spring Boot CLI，[源码](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-cli/src/main/java/org/springframework/boot/cli)里也有丰富的文档说明。

如果发现已触及CLI工具的限制，你可以将应用完全转换为Gradle或Maven构建的groovy工程。下一章节将覆盖Spring Boot的[构建工具插件](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins)，这些工具可以跟Gradle或Maven一起使用。
