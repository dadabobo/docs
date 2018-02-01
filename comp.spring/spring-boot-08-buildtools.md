## 构建工具插件 (Build tool plugins)

Spring Boot为Maven和Gradle提供构建工具插件，该插件提供各种各样的特性，包括打包可执行jars。本章节提供关于插件的更多详情及用于扩展一个不支持的构建系统所需的帮助信息。如果你是刚刚开始，那可能需要先阅读 ***Part III, Using Spring Boot*** 章节的 “***Build systems***”。

### 1. Spring Boot Maven插件

Spring Boot Maven插件为Maven提供Spring Boot支持，它允许你打包可执行jar或war存档，然后就地运行应用。为了使用它，你需要使用Maven 3.2 （或更高版本）。

>参考 [Spring Boot Maven Plugin Site](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/maven-plugin/) 可以获取全部的插件文档。

#### 1.1 包含该插件

想要使用Spring Boot Maven插件只需简单地在你的`pom.xml`的 `plugins` 部分包含相应的XML：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- ... -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.0.0.BUILD-SNAPSHOT</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
该配置会在Maven生命周期的 `package` 阶段重新打包一个jar或war。下面的示例展示在 `target` 目录下既有重新打包后的jar，也有原始的jar：
```
$ mvn package
$ ls target/*.jar
target/myproject-1.0.0.jar target/myproject-1.0.0.jar.original
```
如果不包含像上面那样的 `<execution/>`，你可以自己运行该插件（但只有在package目标也被使用的情况），例如：
```
$ mvn package spring-boot:repackage
$ ls target/*.jar
target/myproject-1.0.0.jar target/myproject-1.0.0.jar.original
```
如果使用一个里程碑或快照版本，你还需要添加正确的 `pluginRepository` 元素：
```xml
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
```

#### 1.2 打包可执行jar和war文件

一旦 `spring-boot-maven-plugin` 被包含到你的 `pom.xml` 中，Spring Boot就会自动尝试使用 `spring-boot:repackage` 目标重写存档以使它们能够执行。为了构建一个jar或war，你应该使用常规的 `packaging` 元素配置你的项目：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- ... -->
    <packaging>jar</packaging>
    <!-- ... -->
</project>
```
生成的存档在 `package` 阶段会被Spring Boot增强。你想启动的main类即可以通过指定一个配置选项，也可以通过为manifest添加一个 `Main-Class` 属性这种常规的方式实现。如果你没有指定一个main类，该插件会搜索带有 `public static void main(String[] args)` 方法的类。

为了构建和运行一个项目的artifact，你可以输入以下命令：
```
$ mvn package
$ java -jar target/mymodule-0.0.1-SNAPSHOT.jar
```
为了构建一个即可执行，又能部署到外部容器的war文件，你需要标记内嵌容器依赖为"provided"，例如：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- ... -->
    <packaging>war</packaging>
    <!-- ... -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        <!-- ... -->
    </dependencies>
</project>
```
>具体参考 “[Section 81.1, “Create a deployable war file”](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#howto-create-a-deployable-war-file)” 章节。

高级配置选项和示例可在[插件信息页面](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/maven-plugin/)获取。


### 2. Spring Boot Gradle插件

Spring Boot Gradle插件为Gradle提供Spring Boot支持，它允许你打包可执行jar或war存档，运行Spring Boot应用，使用 `spring-boot-dependencies` 提供的依赖管理。

[本章详细内容](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-gradle-plugin) 包括：
* 包含该插件
* Gradle依赖管理
* 打包可执行jar和war文件
* 就地（in-place）运行项目
* Spring Boot插件配置
* Repackage配置
* 使用Gradle自定义配置进行Repackage
* 理解Gradle插件是如何工作的
* 使用Gradle将artifacts发布到Maven仓库


### 3. Spring Boot AntLib模块

Spring Boot AntLib模块为Apache Ant提供基本的Spring Boot支持，你可以使用该模块创建可执行的jars。在 `build.xml` 添加额外的 `spring-boot` 命名空间就可以使用该模块了。  
你需要记得在启动Ant时使用 -lib 选项。  
[本章详细内容](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-antlib) 包括：
* Spring Boot Ant任务
* spring-boot:findmainclass

### 4. 对其他构建系统的支持

如果想使用除了Maven和Gradle之外的构建工具，你可能需要开发自己的插件。可执行jars需要遵循一个特定格式，并且一些实体需要以不压缩的方式写入（详情查看附录中的[可执行jar格式](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#executable-jar)章节）。

Spring Boot Maven和Gradle插件在实际生成jars的过程中会使用 `spring-bootloader-tools` ，如果需要，你也可以自由地使用该library。

[本章详细内容](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-other-build-systems) 包括：
* 重新打包存档
* 内嵌库
* 查找main类
* repackage实现示例

### 5. 接下来阅读什么

如果对构建工具插件如何工作感兴趣，你可以查看GitHub上的 [spring-boot-tools](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools) 模块，附加中有详细的[可执行jar格式](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#executable-jar)。

如果有特定构建相关的问题，可以查看[how-to](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#howto)指南。
