# Spring in Action

## 内容概要

Spring框架是以简化Java EE应用程序的开发为目标而创建的。望通过现实中的实际示例代码来为Java EE开发人员展现Spring框架。因为Spring是一个模块化的框架，所以这本书也是按照这种方式编写的。

* 第1部分 介绍Spring框架的核心知识。
* 第2部分 在此基础上介绍如何使用Spring构建Web应用程序。
* 第3部分 告别前端，介绍如何在应用程序的后端使用Spring。
* 第4部分 描述如何使用Spring与其他的应用和服务进行集成。

**第1部分 Spring的核心**

学习Spring容器、依赖注入（dependency injection，DI）和面向切面编程（aspect-oriented programming，AOP），也就是Spring框架的核心。理解Spring的基础原理，而这些原理将会在本书各个章节都会用到。
* 第1章 将会概要地介绍Spring，包括DI和AOP的一些基本样例。同时，读者还会了解到更大的Spring生态系统的整体情况。
* 第2章 更为详细地介绍DI，展现应用程序中的各个组件（bean）如何装配在一起。这包括基于XML装配、基于Java装配以及自动装配。
* 第3章 会介绍几种高级装配技术，读者可能并不会经常用到这些技术，但是如果用到的话，本章的内容将会告诉读者如何发挥Spring容器最强大的威力。
* 第4章 介绍如何使用Spring的AOP来为对象解耦那些对其提供服务的横切性关注点。这一章也为后面各章提供基础，在后面读者将会使用 AOP来提供声明式服务，如事务、安全和缓存。

**第2部分 Web中的Spring**

如何使用Spring来构建Web应用程序。
* 第5章 介绍使用Spring MVC的基础知识，这是Spring中的基础Web框架。读者将会看到如何编写控制器来处理请求，并使用模型数据产 生响应。 当控制器的工作完成后，模型数据必须要使用一个视图来进行渲染。
* 第6章 将会探讨在Spring中可以使用的各种视图技术，包括JSP、 Apache Tiles以及Thymeleaf。
* 第7章 的内容不再是Spring MVC的基础知识了，在本章中，读者将会学习到如何自定义Spring MVC配置、处理multipart类型的文件上 传、处理在控制器中可能会出现的异常并且会通过flash属性在请求之间传递数据。
* 第8章 将会介绍Spring Web Flow，这是Spring MVC的一个扩展，能够开发会话式的Web应用程序。在本章中，读者将会学习到如何构建 引导用户完成特定流程的Web应用程序。
* 第9章 读者将会学到如何使用Spring Security为自己的应用程序Web层实现安全性。

**第3部分 后端中的Spring**

所关注的内容不再是应用程序的前端了，而是关注于如何处理和持久化数据。
* 第10章 首先会介绍如何使用Spring对JDBC的抽象实现关系型数据库中的数据持久化。
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

-----

## 第一部分 Spring的核心

Spring的主要特性为注入依赖（DI）和面向切面编程（AOP）。

### Spring之旅

学习Spring容器、依赖注入（dependency injection，DI）和面向切面编程（aspect-oriented programming，AOP），也就是Spring框架的核心。理解Spring的基础原理，而这些原理将会在本书各个章节都会用到。

介绍Spring的注入依赖（DI）和面向切面编程（AOP）。

#### 简化Java开发

Spring提供了更加轻量级和简单的编程模型。  
Spring竭力避免因自身的API而弄乱你的应用代码。Spring不会强迫你实现Spring规范的接口或继承Spring规范的类。  
为了降低Java开发的复杂性，Spring采取了以下4种关键策略：
* 基于POJO的轻量级和最小侵入性编程；
* 通过依赖注入和面向接口实现松耦合；
* 基于切面和惯例进行声明式编程；
* 通过切面和模板减少样板式代码

**注入依赖**

注入依赖是一种编程技巧或设计模式理念。  
通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。对象无需自行创建或管理它们的依赖关系，依赖关系将被自动注入到需要它们的对象当中去。  
如果一个对象只通过接口（而不是具体实现或初始化过程）来表明依赖关系，那么这种依赖就能够在对象本身毫不知情的情况下，用不同的具体实现进行替换。  

创建应用组件之间协作的行为通常称为 **装配(wiring)**。Spring有多种装配bean的方式，采用XML是很常见的一种装配方式。
如果XML配置不符合你的喜好的话，Spring还支持使用Java来描述配置。  
不管你使用的是基于XML的配置还是基于Java的配置，DI所带来的收益都是相同的。  
只有Spring通过它的配置，能够了解这些组成部分是如何装配起来的。这样的话，就可以在不改变所依赖的类的情况下，修改依赖关系。  

Spring通过 **应用上下文(Application Context)** 装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。


**应用切面**

DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来。

面向切面编程往往被定义为促使软件系统实现关注点的分离一项技术。系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实 现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。

AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。所造成的结果就是这些组件会具有更高的内聚性并且更加关注自身的业务，完全不需要了解涉及系统服务所带来复杂性。AOP能够确保POJO的简单性。


**使用模板消除样板式代码**

Spring旨在通过模板封装来消除样板式代码。Spring的JdbcTemplate使得执行数据库操作时，避免传统的JDBC样板代码成为了可能。

#### 容纳你的Bean

在基于Spring的应用中，你的应用对象生存于Spring容器（container）中。Spring容器负责创建对象，装配它们，配置它们并管 理它们的整个生命周期，从生存到死亡。

**使用应用上下文**

Spring自带了多种类型的应用上下文。下述为常见的：
* `AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。
* `AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
* `ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类 资源。
* `FileSystemXmlapplicationcontext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。
* `XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。


**bean的的生命周期**
1. Spring对bean进行实例化；
2. Spring将值和bean的引用注入到bean对应的属性中；
3. 如果bean实现了`BeanNameAwar`e接口，Spring将bean的ID传递给`setBeanName()`方法；
4. 如果bean实现了`BeanFactoryAware`接口，Spring将调用`setBeanFactory()`方法，将`BeanFactory`容器实例传入；
5. 如果bean实现了`ApplicationContextAware`接口，Spring将调用`setApplicationContext()`方法，将bean所在的应用上下文的引用传入进来；
6. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessBeforeInitialization()`方法；
7. 如果bean实现了`InitializingBean`接口，Spring将调用它们的`after-PropertiesSet()`方法。类似地，如果bean使用`init-method`声明了初始化方法，该方法也会被调用；
8. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`post-ProcessAfterInitialization()`方法；
9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
10. 如果bean实现了`DisposableBean`接口，Spring将调用它的`destroy()`接口方法。同样，如果bean使用`destroy-method`声明了销 毁方法，该方法也会被调用。

现在你已经了解了如何创建和加载一个Spring容器。但是一个空的容器并没有太大的价值，在你把东西放进去之前，它里面什么都没有。为了 从Spring的DI中受益，我们必须将应用对象装配进Spring容器中。我们将在第2章对bean装配进行更详细的探讨

#### 俯瞰Spring风景线

**Spring模块**
* **Spring核心容器**:  
  容器是Spring框架最核心的部分，它管理着Spring应用中bean的创建、配置和管理。在该模块中，包括了Spring bean工厂，它为Spring提供了 DI的功能。基于bean工厂，我们还会发现有多种Spring应用上下文的实现，每一种都提供了配置Spring的不同方式。   
  除了bean工厂和应用上下文，该模块也提供了许多企业服务，例如E-mail、JNDI访问、EJB集成和调度。   
  所有的Spring模块都构建于核心容器之上。当你配置应用时，其实你隐式地使用了这些类。  
* **Spring的的AOP模块**:  
  在AOP模块中，Spring对面向切面编程提供了丰富的支持。这个模块是Spring应用系统中开发切面的基础。与DI一样，AOP可以帮助应用对象 解耦。借助于AOP，可以将遍布系统的关注点（例如事务和安全）从它们所应用的对象中解耦出来。   
* **数数据访问与集成**:  
  使用JDBC编写代码通常会导致大量的样板式代码，例如获得数据库连接、创建语句、处理结果集到最后关闭数据库连接。Spring的JDBC和 DAO（Data Access Object）模块抽象了这些样板式代码，使我们的数据库代码变得简单明了，还可以避免因为关闭数据库资源失败而引发的问题。该模块在多种数据库服务的错误信息之上构建了一个语义丰富的异常层，以后我们再也不需要解释那些隐晦专有的SQL错误信息了！  
  对于那些更喜欢ORM（Object-Relational Mapping）工具而不愿意直接使用JDBC的开发者，Spring提供了ORM模块。Spring的ORM模块建立 在对DAO的支持之上，并为多个ORM框架提供了一种构建DAO的简便方式。Spring没有尝试去创建自己的ORM解决方案，而是对许多流行的 ORM框架进行了集成，包括Hibernate、Java Persisternce API、Java Data Object和iBATIS SQL Maps。Spring的事务管理支持所有的ORM框 架以及JDBC。
  本模块同样包含了在JMS（Java Message Service）之上构建的Spring抽象层，它会使用消息以异步的方式与其他应用集成。从Spring 3.0开 始，本模块还包含对象到XML映射的特性，它最初是Spring Web Service项目的一部分。  
  除此之外，本模块会使用Spring AOP模块为Spring应用中的对象提供事务管理服务。
* **Web与与远程调用**:  
  MVC（Model-View-Controller）模式是一种普遍被接受的构建Web应用的方法，它可以帮助用户将界面逻辑与应用逻辑分离。Java从来不缺少 MVC框架，Apache的Struts、JSF、WebWork和Tapestry都是可选的最流行的MVC框架。  
  虽然Spring能够与多种流行的MVC框架进行集成，但它的Web和远程调用模块自带了一个强大的MVC框架，有助于在Web层提升应用的松耦 合水平。在第5章到第7章中，我们将会学习Spring的MVC框架。  
  除了面向用户的Web应用，该模块还提供了多种构建与其他应用交互的远程调用方案。Spring远程调用功能集成了RMI（Remote Method Invocation）、Hessian、Burlap、JAX-WS，同时Spring还自带了一个远程调用框架：HTTP invoker。Spring还提供了暴露和使用REST API的良好支持。
* **Instrumentation**:  
  Spring的Instrumentation模块提供了为JVM添加代理（agent）的功能。具体来讲，它为Tomcat提供了一个织入代理，能够为Tomcat传递类文件，就像这些文件是被类加载器加载的一样。
* **测试**:  
  鉴于开发者自测的重要性，Spring提供了测试模块以致力于Spring应用的测试。  
  通过该模块，你会发现Spring为使用JNDI、Servlet和Portlet编写单元测试提供了一系列的mock对象实现。对于集成测试，该模块为加载Spring 应用上下文中的bean集合以及与Spring上下文中的bean进行交互提供了支持。

**Spring Portfolio**
* **Spring Web Flow**：Spring Web Flow建立于Spring MVC框架之上，它为基于流程的会话式Web应用提供了支持。
* **Spring Web Service**：Spring Web Service提供了契约优先的Web Service模型，服务的实现都 是为了满足服务的契约而编写的。
* **Spring Security**：利用Spring AOP，Spring Security为Spring应用提供了声明式的安全机制。
* **Spring Integration**：Spring Integration提供了多种通用应用集成模式的Spring声明式风格实现。
* **Spring Batch**：如果需要开发一个批处理应用，你可以通过Spring Batch，
* **Spring Data**：Spring Data使得在Spring中使用任何数据库都变得非常容易。
* **Spring Social**：Spring Social更多的是关注连接（connect），而不是社交（social）。它能
* **Spring Mobile**：Spring Mobile是Spring MVC新的扩展模 块，用于支持移动Web应用开发。
* **Spring for Android**：旨在通过Spring框架为开发基于Android设备的本地应用提供某些简单的支持。
* **Spring Boot**：Spring Boot大量依赖于自动配置技术，它能够消除大部分Spring配置。

#### Spring的新功能
