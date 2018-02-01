## Spring Boot执行器：Production-ready特性

Spring Boot包含很多其他特性，可用来帮你监控和管理发布到生产环境的应用。你可以选择使用HTTP端点，JMX，甚至通过远程shell（SSH或Telnet）来管理和监控应用。审计（Auditing），健康（health）和数据采集（metrics gathering）会自动应用到你的应用。

Actuator HTTP端点只能用在基于Spring MVC的应用，特别地，它不能跟Jersey一块使用，[除非你也启用Spring MVC](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#howto-use-actuator-with-jersey)。

### 1. 开启production-ready特性

`[spring-boot-actuator](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator)`模块提供了Spring Boot所有的production-ready特性。启用该特性的最简单方式就是添加对 `spring-boot-starter-actuator` ‘Starter POM’的依赖。

>**执行器（Actuator）的定义**：执行器是一个制造业术语，指的是用于移动或控制东西的一个机械装置。一个很小的改变就能让执行器产生大量的运动。

基于Maven的项目想要添加执行器只需添加下面的'starter'依赖：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

对于Gradle，使用下面的声明：
```
dependencies {
    compile("org.springframework.boot:spring-boot-starter-actuator")
}
```

### 2. 端点

执行器端点允许你监控应用及与应用进行交互。Spring Boot包含很多内置的端点，你也可以添加自己的。例如，`health`端点提供了应用的基本健康信息。

端点暴露的方式取决于你采用的技术类型。大部分应用选择HTTP监控，端点的ID映射到一个URL。例如，默认情况下，`health`端点将被映射到`/health`。

下面的端点都是可用的：

>根据一个端点暴露的方式，sensitive参数可能会被用做一个安全提示。例如，在使用HTTP访问sensitive端点时需要提供用户名/密码（如果没有启用web安全，可能会简化为禁止访问该端点）。

#### 2.1. 自定义端点 Customizing endpoints

#### 2.2. Hypermedia for actuator MVC endpoints

#### 2.3. CORS support

#### 2.4. Adding custom endpoints

#### 2.5. Health information

#### 2.6. 安全与HealthIndicators
**2.6.1. 自动配置的HealthIndicators**
**2.6.2. 编写自定义HealthIndicators**

#### 2.7. Application information

**2.7.1. 自动配置的InfoContributors**

Spring Boot会在合适的时候自动配置以下InfoContributors：
* `EnvironmentInfoContributor`: 暴露 `Environment` 中key为 `info` 的所有key
* `GitInfoContributor`: 暴露git信息，如果存在 `git.properties` 文件
* `BuildInfoContributor`: 暴露构建信息，如果存在	`METAINF/build-info.properties`	文件

>使用 `management.info.defaults.enabled` 属性可禁用以上所有 `InfoContributors`。


**2.7.2. 自定义应用info信息**

通过设置Spring属性 `info.*`，你可以定义 `info` 端点暴露的数据。所有在 `info` 关键字下的 `Environment` 属性都将被自动暴露，例如，你可以将以下配置添加到 `application.properties`：
```
info.app.encoding=UTF-8
info.app.java.source=1.8
info.app.java.target=1.8
```

注	你可以在[构建时扩展info属性]()，而不是硬编码这些值。假设使用Maven，你可以 按以下配置重写示例：
```
info.app.encoding=@project.build.sourceEncoding@
info.app.java.source=@java.version@
info.app.java.target=@java.version@
```

**2.7.3. Git提交信息**
info 端点的另一个有用特性是，在项目构建完成后发布 git 源码仓库的状态信息。如果 GitProperties bean可用，Spring Boot将暴露 git.branch	，git.commit.id 和 git.commit.time 属性。

>如果classpath根目录存在	git.properties	文件，Spring	Boot将自动配 置	GitProperties		bean。查看Generate	git	information获取更多详细信息。

使用 management.info.git.mode 属性可展示全部git信息（比如 git.properties 全部内容）：  
`management.info.git.mode=full`

**2.7.4. 构建信息**
如果 BuildProperties bean存在，info 端点也会发布你的构建信息。

注	如果classpath下存在 META-INF/build-info.properties 文件，Spring Boot 将自动构建 BuildProperties bean。Maven和Gradle都能产生该文件，具体查 看Generate build information。

**2.7.5. 编写自定义InfoContributors**

你可以注册实现了	InfoContributor	接口的Spring	beans来提供自定义应用信 息。


### 3. 基于HTTP的监控和管理
#### 3.1. 保护敏感端点 Accessing sensitive endpoints
#### 3.2. 自定义管理服务器的上下文路径
#### 3.3. 自定义管理服务器的端口
#### 3.4. 配置管理相关的SSL
#### 3.5. 自定义管理服务器的地址
#### 3.6. 禁用HTTP端点
#### 3.7. HTTP Health端点访问限制

### 4. 基于JMX的监控和管理
#### 4.1. 自定义MBean名称
#### 4.2. 禁用JMX端点
#### 4.3. 使用Jolokia通过HTTP实现JMX远程管理
**4.3.1. 自定义Jolokia**
**4.3.2. 禁用Jolokia**

### 5. 日志
#### 5.1. Configure a Logger

### 6. 度量指标（Metrics）
#### 6.1. 系统指标
#### 6.2. 数据源指标
#### 6.3. 缓存指标
#### 6.4. Tomcat session指标
#### 6.5. 记录自己的指标
#### 6.6. 添加你自己的公共指标
#### 6.7. 指标写入,导出和聚合
**6.7.1. 示例: 导出到Redis**
**6.7.2. 示例: 导出到Open TSDB**
**6.7.3. 示例: 导出到Statsd**
**6.7.4. 示例: 导出到JMX**
#### 6.8. 聚合多个来源的指标
#### 6.9. Dropwizard指标
#### 6.10. 消息渠道集成

### 7. 审计

Spring Boot执行器具有一个灵活的审计框架，一旦Spring Security处于活动状态（默认抛出'authentication success'，'failure'和'access denied'异常），它就会发布事件。这对于报告非常有用，同时可以基于认证失败实现一个锁定策略。可以通过提供自己实现`AbstractAuthenticationAuditListener`和`AbstractAuthorizationAuditListener`来定制发布 security 事件。

你也可以使用审计服务处理自己的业务事件。为此，你可以将存在的`AuditEventRepository`注入到自己的组件，并直接使用它，或者只是简单地通过Spring `ApplicationEventPublisher`发布`AuditApplicationEvent`（使用`ApplicationEventPublisherAware`）。


### 8. 追踪（Tracing）

对于所有的HTTP请求Spring Boot自动启用追踪。你可以查看`trace`端点，并获取最近一些请求的基本信息：
```
[{
    "timestamp": 1394343677415,
    "info": {
        "method": "GET",
        "path": "/trace",
        "headers": {
            "request": {
                "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
                "Connection": "keep-alive",
                "Accept-Encoding": "gzip, deflate",
                "User-Agent": "Mozilla/5.0 Gecko/Firefox",
                "Accept-Language": "en-US,en;q=0.5",
                "Cookie": "_ga=GA1.1.827067509.1390890128; ..."
                "Authorization": "Basic ...",
                "Host": "localhost:8080"
            },
            "response": {
                "Strict-Transport-Security": "max-age=31536000 ; includeSubDomains",
                "X-Application-Context": "application:8080",
                "Content-Type": "application/json;charset=UTF-8",
                "status": "200"
            }
        }
    }
},{
    "timestamp": 1394343684465,
    ...
}]
```

#### 8.1. 自定义追踪
如果需要追踪其他事件，你可以注入`TraceRepository`到你的Spring Beans中，`add`	方法接收一个 `Map` 结构的参数，该数据将转换为JSON并被记录下来。

默认使用	`InMemoryTraceRepository`	存储最新的100个事件，如果需要扩充容量，你可以定义自己的	`InMemoryTraceRepository`	实例，甚至创建自己的	`TraceRepository`	实现。


### 9. 进程监控

在Spring Boot执行器中，你可以找到几个类，它们创建的文件利于进程监控：
* `ApplicationPidFileWriter`创建一个包含应用PID的文件（默认位于应用目录，文件名为`application.pid`）。
* `EmbeddedServerPortFileWriter`创建一个或多个包含内嵌服务器端口的文件（默认位于应用目录，文件名为application.port）。

这些writers默认没被激活，但你可以使用以下描述的任何方式来启用它们。

#### 9.1. 扩展属性
你需要激活`META-INF/spring.factories`文件里的listener(s)：
```
org.springframework.context.ApplicationListener=\
org.springframework.boot.system.ApplicationPidFileWriter,\
org.springframework.boot.actuate.system.EmbeddedServerPortFileWriter
```
#### 9.2. 以编程方式
你也可以通过调用`SpringApplication.addListeners(…)`方法来激活一个监听器，并传递相应的`Writer`对象。该方法允许你通过`Writer`构造器自定义文件名和路径。


### 10. Cloud Foundry支持
#### 10.1. Disabling extended Cloud Foundry actuator support
#### 10.2. Cloud Foundry self signed certificates
#### 10.3. Custom security configuration

### 11. 接下来阅读什么

如果想探索本章节讨论的某些内容，你可以看下执行器的[示例应用](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples)，你也可能想了解图形工具比如[Graphite](http://graphite.wikidot.com/)。

此外，你可以继续了解‘[deployment options](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#deployment)’或直接跳到Spring Boot的[build tool plugins](http://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#build-tool-plugins)。

---
### 使用远程shell进行监控和管理
