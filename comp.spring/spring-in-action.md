
## Spring in Action

**第一部分 Spring的核心**
* Spring之旅
* 装配Bean
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

## 内容概要

Spring框架是以简化Java EE应用程序的开发为目标而创建的。望通过现实中的实际示例代码来为Java EE开发人员展现Spring框架。因为Spring是一个模块化的框架，所以这本书也是按照这种方式编写的。

* 第1部分 介绍Spring框架的核心知识。
* 第2部分 在此基础上介绍如何使用Spring构建Web应用程序。
* 第3部分 告别前端，介绍如何在应用程序的后端使用Spring。
* 第4部分 描述如何使用Spring与其他的应用和服务进行集成。

**第1部分 Spring的核心**

学习Spring容器、依赖注入（dependency injection，DI）和面向切面编程（aspect-oriented programming，AOP），也就是Spring框架的核心。理解Spring的基础原理，而这些原理将会在本书各个章节都会用到。
* 第1章 将会概要地介绍Spring，包括DI和AOP的一些基本样例。同时，读者还会了解到更大的Spring生态系统的整体情况。[第1章](#spring-in-action01)
* 第2章 更为详细地介绍DI，展现应用程序中的各个组件（bean）如何装配在一起。这包括基于XML装配、基于Java装配以及自动装配。[第2章](#spring-in-action02)
* 第3章 会介绍几种高级装配技术，读者可能并不会经常用到这些技术，但是如果用到的话，本章的内容将会告诉读者如何发挥Spring容器最强大的威力。[第3章](#spring-in-action03)
* 第4章 介绍如何使用Spring的AOP来为对象解耦那些对其提供服务的横切性关注点。这一章也为后面各章提供基础，在后面读者将会使用 AOP来提供声明式服务，如事务、安全和缓存。[第4章](#spring-in-action04)

**第2部分 Web中的Spring**

如何使用Spring来构建Web应用程序。
* 第5章 介绍使用Spring MVC的基础知识，这是Spring中的基础Web框架。读者将会看到如何编写控制器来处理请求，并使用模型数据产 生响应。 当控制器的工作完成后，模型数据必须要使用一个视图来进行渲染。[第5章](#spring-in-action05)
* 第6章 将会探讨在Spring中可以使用的各种视图技术，包括JSP、 Apache Tiles以及Thymeleaf。[第6章](#spring-in-action06)
* 第7章 的内容不再是Spring MVC的基础知识了，在本章中，读者将会学习到如何自定义Spring MVC配置、处理multipart类型的文件上 传、处理在控制器中可能会出现的异常并且会通过flash属性在请求之间传递数据。[第7章](#spring-in-action07)
* 第8章 将会介绍Spring Web Flow，这是Spring MVC的一个扩展，能够开发会话式的Web应用程序。在本章中，读者将会学习到如何构建 引导用户完成特定流程的Web应用程序。[第8章](#spring-in-action08)
* 第9章 读者将会学到如何使用Spring Security为自己的应用程序Web层实现安全性。[第9章](#spring-in-action09)

**第3部分 后端中的Spring**

所关注的内容不再是应用程序的前端了，而是关注于如何处理和持久化数据。
* 第10章 首先会介绍如何使用Spring对JDBC的抽象实现关系型数据库中的数据持久化。[第10章](#spring-in-action10)
* 第11章 从另外一个角度介绍数据持久化，也就是使用Java持久化API（JPA）存储关系型数据库中的数据。
* 第12章 将会介绍如何将Spring与非关系型数据库结合使用，如MongoDB和Neo4j。 不管数据存储在什么地方，缓存都有助于性能的提升，这是通过只有在必要的时候才去查询数据库实现的。
* 第13章 将会为读者介绍 Spring对声明式缓存的支持。
* 第14章 重新回到Spring Security，将会介绍如何通过AOP将安全性应用到方法级别。

**第四部分 Spring集成**

本书的最后一部分会介绍如何将Spring应用程序与其他系统进行集成。
* 第15章 将会学习如何创建与使用远程服务，包括RMI、Hessian、Burlap以及基于SOAP的服务。
* 第16章 将会再次回到Spring MVC，我们将会看到如何创建RESTful服务，在这个过程中所使用的编程模型与之前在第5章中所描述的是一 致的。
* 第17章 将会探讨Spring对异步消息的支持，本章将会包括Java消息服务（Java Message Service，JMS）以及高级消息队列协议 （Advanced Message Queuing Protocol，AMQP）。
* 第18章 异步消息有了新的花样，在这一章中读者会看到如何将Spring与WebSocket和STOMP结合起来，实现服务端与客户端之间 的异步通信。
* 第19章 将会介绍如何使用Spring发送E-mail。
* 第20章 会关注于Spring对Java管理扩展（Java Management Extensions，JMX）功能的支持，借助这项功能可以对Spring应用程序进行 监控和修改运行时配置。
* 第21章 读者将会看到一个全新并且会改变游戏规则的Spring使用方式，名为Spring Boot。我们将会看到Spring Boot如何将 Spring应用中样板式的配置移除掉，这样就能让读者更加专注于业务功能。


**新变化**
* 强调基于Java的Spring配置，基于Java的配置方案几乎可以用在所有Spring开发领域之中；
* 条件化的配置以及profile特性能够让Spring在运行时确定该使用或忽略哪些Spring配置；
* Spring MVC的多项增强和改善，尤其是与创建REST服务相关的；
* 在Spring应用中使用Thymeleaf替代JSP；
* 使用基于Java的配置启用Spring Security；
* 使用Spring Data，在运行时自动为JPA、MongoDB和Neo4j生成Repository实现；
* Spring新提供的声明式缓存支持；
* 借助WebSocket和STOMP，实现异步的Web消息；
* Spring Boot，改变使用Spring游戏规则的新方法


<span id="spring-in-action01"></span>
@import "spring-in-action01.md"

<span id="spring-in-action02"></span>
@import "spring-in-action02.md"

<span id="spring-in-action03"></span>
@import "spring-in-action03.md"

<span id="spring-in-action04"></span>
@import "spring-in-action04.md"

<span id="spring-in-action05"></span>
@import "spring-in-action05.md"

<span id="spring-in-action06"></span>
@import "spring-in-action06.md"

<span id="spring-in-action07"></span>
@import "spring-in-action07.md"

<span id="spring-in-action08"></span>
@import "spring-in-action08.md"

<span id="spring-in-action09"></span>
@import "spring-in-action09.md"

<span id="spring-in-action10"></span>
@import "spring-in-action10.md"
