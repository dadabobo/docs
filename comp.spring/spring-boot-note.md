## Spring Boot

Spring Boot简化了基于Spring的应用开发，你只需要"run"就能创建一个独立的，产品级别的Spring应用。 我们为Spring平台及第三方库提供开箱即用的设置，这样你就可以有条不紊地开始。多数Spring Boot应用只需要很少的Spring配置。  

你可以使用Spring Boot创建Java应用，并使用`java -jar`启动它或采用传统的war部署方式。我们也提供了一个运行"spring脚本"的命令行工具。   

我们主要的目标是：
* 为所有Spring开发提供一个从根本上更快，且随处可得的入门体验。
* 开箱即用，但通过不采用默认设置可以快速摆脱这种方式。
* 提供一系列大型项目常用的非功能性特征，比如：内嵌服务器，安全，指标，健康检测，外部化配置。
* 绝对没有代码生成，也不需要XML配置。


### Spring Boot 参考文档

**第一部分 Spring Boot文档** [详细内容](#spring-boot-doc)
* 关于本文档
* 获取帮助
* 第一步 (First steps)
* 使用Spring Boot
* 了解Spring Boot特性
* 迁移到生产环境
* 高级主题

**第二部分 开始**  [详细内容](#spring-boot-gettingstarted)

如果你想从大体上了解Spring Boot或Spring，本章节正是你所需要的！本节中，我们会回答基本的"what?"，"how?"和"why?"等问题，并通过一些安装指南简单介绍 下Spring Boot。然后我们会构建第一个Spring Boot应用，并讨论一些需要遵循的核心原则。
* Spring Boot介绍
* 系统要求
* Spring Boot 安装
* 开发第一个Spring Boot应用

**第三部分 使用Spring Boot** [详细内容](#spring-boot-usingspringboot)

介绍如何使用Spring Boot，不仅覆盖构建系统，自动配置，如何运行应用等主题，还包括一些Spring Boot的最佳实践。
* 系统构建
* 组织代码
* 配置类
* 自动配置
* Spring Beans和依赖注入
* 使用@SpringBootApplication注解
* 运行应用程序
* 开发者工具
* 打包用于生产的应用

**第四部分 Spring Boot特性** [详细内容](#spring-boot-features)

深入详细的介绍Spring Boot，通过阅读你可以了解到需要使用和定制的核心特性。
* SpringApplication
* 外部化配置
* Profiles
* 开发Web应用
* 日志
* 安全
* 使用SQL数据库
* 使用NoSQL技术
* 缓存
* 消息
* 调用REST服务
* Validation
* 发送邮件
* 使用JTA处理分布式事务
* Hazelcast
* Spring集成
* Spring Session
* 基于JMX的监控和管理
* 测试
* WebSockets
* Web Services
* 创建自己的auto-configuration

**第五部分 Spring Boot执行器: Production-ready特性** [详细内容](#spring-boot-actuator)

* 开启production-ready特性
* 端点
* 基于HTTP的监控和管理
* 基于JMX的监控和管理
* 使用远程shell进行监控和管理
* 度量指标（Metrics）
* 审计
* 追踪（Tracing）

**第六部分 部署Spring Boot应用** [详细内容](#spring-boot-deploy)

Spring Boot灵活的打包选项为部署应用提供多种选择，你可以轻易的将Spring Boot应用部署到各种云平台，容器镜像（比如Docker）或虚拟/真实机器。本章节覆盖一些比较常见的部署场景。
* 部署到云端
* 安装Spring Boot应用

**第七部分 Spring Boot CLI** [详细内容](#spring-boot-cli)

Spring Boot CLI是一个命令行工具，如果想使用Spring进行快速开发可以使用它。它允许你运行Groovy脚本，这意味着你可以使用熟悉的类Java语法，并且没有那么多的模板代码。你可以通过Spring Boot CLI启动新项目，或为它编写命令。
* 安装CLI
* 使用CLI
* 使用Groovy beans DSL开发应用
* 使用settings.xml配置CLI

**第八部分 构建工具插件** [详细内容](#spring-boot-buildtools)

Spring Boot为Maven和Gradle提供构建工具插件，该插件提供各种各样的特性，包括打包可执行jars。本章节提供关于插件的更多详情及用于扩展一个不支持的构建系统所需的帮助信息。如果你是刚刚开始，那可能需要先阅读 “第三部分 使用Spring Boot” 的 “构建系统”。
* Spring Boot Maven插件
* Spring Boot Gradle插件
* Spring Boot AntLib模块
* 对其他构建系统的支持

**第九部分 How-to指南**  [详细内容](#spring-boot-howto)

本章节将回答一些常见的"我该怎么做"类型的问题，这些问题在我们使用Spring Boot时经常遇到。这虽然不是一个详尽的列表，但它覆盖了很多方面。

如果遇到一个特殊的我们没有覆盖的问题，你可以查看stackoverflow.com，看是否已经有人给出了答案；这也是一个很好的提新问题的地方（请使用 springboot标签）。
* Spring Boot应用
* 属性与配置
* 内嵌servlet容器
* Spring MVC
* HTTP 客户端
* 日志
* 数据访问
* 数据库初始化
* Messaging
* 批处理应用
* 执行器（Actuator）
* 安全
* 热交换
* 构建
* 传统部署


**第十部分 附录** [详细内容](#spring-boot-append)
* 常见应用属性
* 配置元数据
* 自动配置类
* 测试自动配置注解
* 可执行jar格式
* 依赖版本


<span id="spring-boot-doc"> </span>
@import "spring-boot-01-doc.md"

<span id="spring-boot-gettingstarted"> </span>
@import "spring-boot-02-gettingstarted.md"

<span id="spring-boot-usingspringboot"> </span>
@import "spring-boot-03-usingspringboot.md"

<span id="spring-boot-features"> </span>
@import "spring-boot-04-features.md"

<span id="spring-boot-actuator"> </span>
@import "spring-boot-05-actuator.md"

<span id="spring-boot-deploy"> </span>
@import "spring-boot-06-deploy.md"

<span id="spring-boot-cli"> </span>
@import "spring-boot-07-cli.md"

<span id="spring-boot-buildtools"> </span>
@import "spring-boot-08-buildtools.md"

<span id="spring-boot-howto"> </span>
@import "spring-boot-09-howto.md"

<span id="spring-boot-append"> </span>
@import "spring-boot-10-append.md"
