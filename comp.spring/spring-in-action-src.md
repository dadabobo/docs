
## Spring in Action 代码

项目使用Gradle构建工具，项目布局参考 `project-struct.md`

`$ProjectRoot` 分为三大部分组成：gradle构建文件、项目代码和构建输出。
* `gradle`: 使用Gradle构建的每个项目均需要
* `src`：项目内容，代码、配置、测试
* `build`：构建输出

```
build.gradle
gradlew
gradlew.bat
gradle/wrapper/
    gradle-wrapper.jar
    gradle-wrapper.properties

src/
src/main/java/
src/main/resources/
src/main/test/java/
src/main/test/resources/
src/site

build/
```


### 第一部分 Spring的核心

#### Chapter 01 Spring之旅

`Chapter_01/knight`
````
src/main/java/sia/knights
    Knight.java                     # Knight接口
    DamselRescuingKnight.java       # Knight接口实现(与RescueDamselQuest紧耦合)    
    BraveKnight.java                # Knight接口实现(构造器注入 Quest)
    Quest.java                      # Quest接口
    RescueDamselQuest.java          # Quest接口实现
    SlayDragonQuest.java            # Quest接口实现()
    KnightMain.java                 # 加载包含Knight的Spring上下文(knight.xml/minstrel.xml)
    Minstrel.java                   # Minstrel类(AOP)
src/main/java/sia/knights/config
    KnightConfig.java               # 基于Java的配置
    SoundSystemConfig.java          #

src/main/resources
    log4j.properties                # log4j配置
src/main/resources/META-INF/spring
    knight.xml                      # 使用Spring将SlayDragonQuest注入到BraveKnight中
    minstrel.xml                    # 将Minstrel声明为一个切面

src/site
     site.xml

src/test/java/sia/knights
    BraveKnightTest.java            # 测试BraveKnight
    FakePrintStream.java            # PrintStream
    KnightConfig.java               # Java配置
    KnightJavaConfigInjectionTest.java  # 测试基于Java的配置     
    KnightXMLInjectionTest.java     # 测试基于XML的配置
src/test/resources
    log4j.properties                # log4j配置
src/test/resources/sia/knights      #
    KnightXMLInjectionTest-context.xml  # 注入依赖XML的配置文件     
````


#### Chapter 02 S装配Bean
`Chapter_02/stereo-autoconfig`
````
src/main/java/soundsystem
    MediaPlayer.java                # MediaPlayer接口
    CDPlayer.java                   # 实现MediaPlayer接口 @Component @Autowired
    CompactDisc.java                # CompactDisc接口  @Component @Autowired
    SgtPeppers.java                 # 实现CompactDisc接口
    CDPlayerConfig.java             # 启动注册组件扫描 @ComponentScan @Configuration

src/main/resources/META-INF/spring
    soundsystem.xml                 # XML启用组件扫描    

src/test/java/soundsystem
    CDPlayerTest.java               # 测试自动化装配
    CDPlayerXMLConfigTest.java      # 测试XML装配
````


`Chapter_02/stereo-javaconfig`
````

````

`Chapter_02/stereo-xmlconfig`
````
src/main/java/soundsystem
    BlankDisc.java
    CDPlayer.java
    CDPlayerConfig.java
    CompactDisc.java
    MediaPlayer.java
    SgtPeppers.java
src/main/java/soundsystem/collections
    BlankDisc.java
src/main/java/soundsystem/properties
    BlankDisc.java
    CDPlayer.java
src/test/java/soundsystem
    CNamespaceReferenceTest.java
src/test/resources/soundsystem
    CNamespaceReferenceTest-context.xml
    
````

`Chapter_02/stereo-mixedconfig`
````
````

----

*
* 高级装配
* 面向切面的Spring

**第二部分 Web中的Spring**
* 构建Spring Web应用
* 渲染Web视图
* Spring MVC的高级技术
* 使用Spring Web Flow
* 保护Spring 应用

**第三部分 后端中的Spring**
* 通过Spring和JDBC征服数据库
* 使用对象-关系映射持久数据库
* 使用NoSQL数据库
* 缓存数据
* 保护方法应用

**第四部分 Spring集成**
* 使用远程服务
* 使用Spring MVC创建REST API
* Spring消息
* 使用WebSocket和STOMP实现消息功能
* 使用Spring发送Email
* 使用JMX管理Spring Bean
* 借助Spring Boot简化Spring开发

---
