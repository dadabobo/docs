
## 项目目录结构

#### Eclipse项目目录结构
`ProjectRoot`
```
+ model
    + .classpath
    + .project
    + .settings
+ repository
    + .classpath
    + .project
    + .settings
+ web
    + .classpath
    + .project
    + .settings
+ .classpath
+ .project
+ .settings
```
Eclipse项目目录结构说明：
* `.project` - 项目的基本信息，包括：名字、描述、其他项目或资源的引用和项目类型。
* `.classpath` - 所应用的外部依赖库和其他项目清单
* `.settings` - 可选。包含特定工作空间的相关设置。保存了像Java编译器版本，源代码版本合规等设置。


#### Maven项目布局
`ProjectRoot`
```
+ pom.xml
+ src
    + main
        + java
        + resources
        + resources-filtered
        + resources-filters
        + filters
        + webapp
    + test
        + java
        + resources
        + resources-filtered
        + resources-filters
        + filters
    + it
    + assembly
    + site
+ target
+ LICENSE.txt
+ NOTICE.txt
+ README.txt
```

#### Gradle项目布局
`ProjectRoot`
```
+ build.gradle
+ gradlew
+ gradlew.bat
+ gradle
    + wrapper
        + gradle-wrapper.jar
        + gradle-wrapper.properties
+ src
    + main
        + java
        + resources
    + test
        + java
        + resources
+ build
    + classes
    + dependency-cache
    + libs
        + myapp.jar
    + reports
        + tests
    + test-results
    + tmp
```

**应用代码目录结构**  
`src/main/java`
```
+ com/dsl/prj
    + todo
        + ToDoApp.java
        + model
            + ToDoItem.java
        + repository
            + InMemoryToDoRepository.java
            + ToDoRepository.java
        + utils
            + CommandLineInput.java
            + CommandLineInputHandler.java

```
