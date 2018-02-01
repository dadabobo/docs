## Spring Boot 附录

### 附录A. 常见应用属性

你可以在	application.properties/application.yml	文件内部或通过命令行开关来指定各种属性。本章节提供了一个常见Spring Boot属性的列表及使用这些属性的底层类的引用。

>属性可以来自classpath下的其他jar文件中，所以你不应该把它当成详尽的列 表。定义你自己的属性也是相当合法的。  

>示例文件只是一个指导。不要拷贝/粘贴整个内容到你的应用，而是只提取你需 要的属性。

### 附录B. 配置元数据

Spring	Boot	jars包含元数据文件，它们提供了所有支持的配置属性详情。这些文件 设计用于让IDE开发者能够为使 用	application.properties 或 application.yml	文件的用户提供上下文帮助 及代码完成功能。

主要的元数据文件是在编译器通过处理所有被 @ConfigurationProperties 注解的节点来自动生成的。

### 附录C. 自动配置类
这里有一个Spring	Boot提供的所有自动配置类的文档链接和源码列表。也要记着看 一下你的应用都开启了哪些自动配置（使用	--debug	或	-Debug	启动应用，或在 一个Actuator应用中使用	autoconfig	端点）。


### 附录D. 测试自动配置注解

### 附录E. 可执行jar格式

spring-boot-loader 模块允许Spring Boot对可执行jar和war文件的支持。如果 你正在使用Maven或Gradle插件，可执行jar会被自动产生，通常你不需要了解它是 如何工作的。

如果你需要从一个不同的构建系统创建可执行jars，或你只是对底层技术好奇，本 章节将提供一些背景资料。

#### 附录E.1. 内嵌JARs

Java没有提供任何标准的方式来加载内嵌的jar文件（也就是jar文件自身包含到一个jar中）。如果你正分发一个在不解压缩的情况下可以从命令行运行的自包含应用，那这将是个问题。

为了解决这个问题，很多开发者使用"影子" jars。一个影子jar只是简单的将所有jars 的类打包进一个单独的"超级jar"。使用影子jars的问题是它很难分辨在你的应用中实际可以使用的库。在多个jars中存在相同的文件名（内容不同）也是一个问题。 Spring Boot另劈稀径，让你能够直接嵌套jars。

**附录E.1.1	可执行jar文件结构**

Spring Boot Loader兼容的jar文件应该遵循以下结构：
```
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-BOOT-INF
    +-classes
    |  +-mycompany
    |     +-project
    |        +-YourClasses.class
    +-lib
       +-dependency1.jar
       +-dependency2.jar
```
应用类放到内部的 `BOOT-INF/classes`  目录下，依赖需要放到内部的 `BOOT-INF/lib` 目录下。

**附录E.1.2. 可执行war文件结构**

Spring Boot Loader兼容的war文件应该遵循以下结构：
```
example.war
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-WEB-INF
    +-classes
    |  +-com
    |     +-mycompany
    |        +-project
    |           +-YourClasses.class
    +-lib
    |  +-dependency1.jar
    |  +-dependency2.jar
    +-lib-provided
       +-servlet-api.jar
       +-dependency3.jar
```
依赖需要放到内嵌的	`WEB-INF/lib`	目录下。任何运行时需要但部署到普通web容器不需要的依赖应该放到	`WEB-INF/lib-provided` 目录下。


#### 附录E.2. Spring	Boot的"JarFile"类

#### 附录E.3. 启动可执行jars
#### 附录E.4. PropertiesLauncher特性
#### 附录E.5. 可执行jar的限制
#### 附录E.6. 可替代的单一jar解决方案


### 附录F. 依赖版本
