## Main Projects

From configuration to security, web apps to big data – whatever the infrastructure needs of your application may be, there is a Spring Project to help you build it. Start small and use just what you need – Spring is modular by design.

从配置到安全、web应用到大数据-无论应用需要的什么样的基础设施，总有一个Spring Project能够帮助你构建它。从小处着手，用你需要的--Spring采用模块化的设计。

#### Spring IO Platform
Provides a cohesive, versioned platform for building modern applications. It is a modular, enterprise-grade distribution that delivers a curated set of dependencies.

为构建现代应用提供整合的版本控制平台。 它是模块化的企业级分布系统，提供精心组织的依赖集。

Spring IO Platform它能够结合Maven (或Gradle)管理每个模块的依赖，使得开发者不再花心思研究各个Java库相互依赖的版本，只需要引入Spring IO Platform即可，因为这些库的依赖关系Spring IO Platform已经帮你验证过了。

The Spring IO platform includes Foundation Layer modules and Execution Layer domain-specific runtimes (DSRs). The Foundation layer represents the core Spring modules and associated third-party dependencies that have been harmonized to ensure a smooth development experience. The DSRs provided by the Spring IO Execution Layer dramatically simplify building production-ready, JVM-based workloads. The first release of Spring IO includes two DSRs: Spring Boot and Grails

**Features**
* One platform, many workloads - build web, integration, batch, reactive or big data applications
* Radically simplified development experience with Spring Boot
* Production-ready features provided out of the box
* Curated and harmonized dependencies that just work together
* Modular platform that allows developers to deploy only the parts they need
* Support for embedded runtimes, classic application server, and PaaS deployments
* Depends only on Java SE, and supports Groovy, Grails and some Java EE
* Works with your existing dependency management tools such as Maven and Gradle
* The Spring IO Platform is certified to work on JDK 7 and 8

*While the Spring IO Platform supports JDK 7 and 8, many individual Spring projects also support older JDK versions. Please refer to the [individual projects' documentation] (http://spring.io/docs) for the specific minimum requirements.*

#### Spring Boot

Spring极大地简化了众多的编程任务，减少甚至消除了很多样板式代码，如果没有Spring的话，在日常工作中你不得不编写这样的样板代码。 Spring Boot是一个崭新的令人兴奋的项目，它以Spring的视角，致力于简化Spring本身。

Spring Boot大量依赖于自动配置技术，它能够消除大部分（在很多场景中，甚至是全部）Spring配置。它还提供了多个Starter项目，不管你使用Maven还是Gradle，这都能减少Spring工程构建文件的大小。

Takes an opinionated view of building Spring applications and gets you up and running as quickly as possible.
供简洁的方式构建Spring 应用程序，使你能够尽可能快地运行。

Takes an opinionated view of building production-ready Spring applications. Spring Boot favors convention over configuration and is designed to get you up and running as quickly as possible.

提供简洁的方式构建生产就绪Spring的应用程序。Spring Boot采用约定优先配置，被设计成为了帮助你尽可能快地运行。

Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run". We take an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration.

**Features**
* Create stand-alone Spring applications
* Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
* Provide opinionated 'starter' POMs to simplify your Maven configuration
* Automatically configure Spring whenever possible
* Provide production-ready features such as metrics, health checks and externalized configuration
* Absolutely no code generation and no requirement for XML configuration
The reference guide includes detailed descriptions of all the features, plus an extensive howto for common use cases.


#### Spring Framework
Provides core support for dependency injection, transaction management, web apps, data access, messaging and more.

为注入依赖、事务管理、web应用、数据访问、消息等提供核心支持。

The Spring Framework provides a comprehensive programming and configuration model for modern Java-based enterprise applications - on any kind of deployment platform. A key element of Spring is infrastructural support at the application level: Spring focuses on the "plumbing" of enterprise applications so that teams can focus on application-level business logic, without unnecessary ties to specific deployment environments.

**Features**
* Dependency Injection
* Aspect-Oriented Programming including Spring's declarative transaction management
* Spring MVC web application and RESTful web service framework
* Foundational support for JDBC, JPA, JMS
* Much more…
All avaible features and modules are described in the Modules section of the reference documentation. Their maven/gradle coordinates are also described there.


#### Spring Cloud Data Flow
An orchestration service for composable data microservice applications on modern runtimes.

#### Spring Cloud
Provides a set of tools for common patterns in distributed systems. Useful for building and deploying microservices.

#### Spring Data
Provides a consistent approach to data access – relational, non-relational, map-reduce, and beyond.

Spring Data使得在Spring中使用任何数据库都变得非常容易。尽管关系型数据库统治企业级应用多年，但是现代化的应用正在认识到并不是所 有的数据都适合放在一张表中的行和列中。一种新的数据库种类，通常被称之为NoSQL数据库[2]，提供了使用数据的新方法，这些方法会比传 统的关系型数据库更为合适。

不管你使用文档数据库，如MongoDB，图数据库，如Neo4j，还是传统的关系型数据库，Spring Data都为持久化提供了一种简单的编程模型。 这包括为多种数据库类型提供了一种自动化的Repository机制，它负责为你创建Repository的实现。


#### Spring Integration
Supports the well-known Enterprise Integration Patterns via lightweight messaging and declarative adapters.

许多企业级应用都需要与其他应用进行交互。Spring Integration提供了多种通用应用集成模式的Spring声明式风格实现。

#### Spring Batch
Simplifies and optimizes the work of processing high-volume batch operations.

当我们需要对数据进行大量操作时，没有任何技术可以比批处理更胜任这种场景。如果需要开发一个批处理应用，你可以通过Spring Batch， 使用Spring强大的面向POJO的编程模型。

#### Spring Security
Protects your application with comprehensive and extensible authentication and authorization support.

安全对于许多应用都是一个非常关键的切面。利用Spring AOP，Spring Security为Spring应用提供了声明式的安全机制。你将会在第9章看到如 何为应用的Web层添加Spring Security功能。

#### Spring HATEOAS
Simplifies creating REST representations that follow the HATEOAS principle.

#### Spring Social
Easily connects your applications with third-party APIs such as Facebook, Twitter, LinkedIn, and more.

社交网络是互联网领域中新兴的一种潮流，越来越多的应用正在融入社交网络网站，例如Facebook或者Twitter。如果对此感兴趣，你可以了解 一下Spring Social，这是Spring的一个社交网络扩展模块。

不过，Spring Social并不仅仅是tweet和好友。尽管名字是这样，但Spring Social更多的是关注连接（connect），而不是社交（social）。它能够帮助你通过REST API连接Spring应用，其中有些Spring应用可能原本并没有任何社交方面的功能目标。

#### Spring AMQP
Applies core Spring concepts to the development of AMQP-based messaging solutions.

#### Spring Mobile
Simplifies the development of mobile web apps through device detection and progressive rendering options.

移动应用是另一个引人瞩目的软件开发领域。智能手机和平板设备已成为许多用户首选的客户端。Spring Mobile是Spring MVC新的扩展模块，用于支持移动Web应用开发。

#### Spring for Android
Provides key Spring components for use in developing Android applications.

与Spring Mobile相关的是Spring Android项目。这个新项目，旨在通过Spring框架为开发基于Android设备的本地应用提供某些简单的支持。最初，这个项目提供了Spring RestTemplate的一个可以用于Android应用之中的版本。它还能与Spring Social协作，使得原生应用可以通过 REST API进行社交网络的连接。

#### Spring Web Flow
Supports building web applications with controlled navigation such as checking in for a flight or applying for a loan.

Spring Web Flow建立于Spring MVC框架之上，它为基于流程的会话式Web应用（可以想一下购物车或者向导功能）提供了支持。

#### Spring Web Services
Facilitates the development of contract-first SOAP web services.

虽然核心的Spring框架提供了将Spring bean以声明的方式发布为Web Service的功能，但是这些服务是基于一个具有争议性的架构（拙劣的契 约后置模型）之上而构建的。这些服务的契约由bean的接口来决定。 Spring Web Service提供了契约优先的Web Service模型，服务的实现都 是为了满足服务的契约而编写的。


#### Spring LDAP
Simplifies the development of applications using LDAP using Spring's familiar template-based approach.

#### Spring Session
Spring Session provides an API and implementations for managing a user’s session information.


#### Spring Shell
Provides a powerful foundation for building command-line apps using a Spring-based programming model.

#### Spring XD
Simplifies the development of big data applications by addressing ingestion, analytics, batch jobs and data export.

#### Spring Flo
A JavaScript library that offers a basic embeddable HTML5 visual builder for pipelines and simple graphs.

#### Spring Kafka
Provides Familiar Spring Abstractions for Apache Kafka.


## Community Projects

#### Spring Roo
Makes it fast and easy to build full Java applications in minutes.

#### Spring Scala
Brings the power and expressiveness of Scala together with the productivity and deep ecosystem of Spring.

## Projects in the Attic
#### Spring BlazeDS Integration
Provides first-class support for using Adobe BlazeDS in Spring-based apps with Adobe Flex front-end clients.

#### Spring Loaded
Boosts development productivity by reloading class file changes—as you make them—within your app's JVM.

#### REST Shell
Makes writing and testing RESTful applications easier with CLI-based resource discovery and interaction.
