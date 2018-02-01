
## Spring in Action

## 第三部分 后端中的Spring

## 第十章 通过Spring和JDBC征服数据库

本章内容：
* 定义Spring对数据访问的支持
* 配置数据库资源
* 使用Spring的JDBC模版

在掌握了Spring容器的核心知识之后，是时候将它在实际应用中进行使用了。数据持久化是一个非常不错的起点，因为几乎所有的企业级应用 程序中都存在这样的需求。我们可能都处理过数据库访问功能，在实际的工作中也发现数据访问有一些不足之处。我们必须初始化数据访问框 架、打开连接、处理各种异常和关闭连接。如果上述操作出现任何问题，都有可能损坏或删除珍贵的企业数据。如果你还未曾经历过因未妥善 处理数据访问而带来的严重后果，那我要提醒你这绝对不是什么好事情。

做事要追求尽善尽美，所以我们选择了Spring。Spring自带了一组数据访问框架，集成了多种数据访问技术。不管你是直接通过JDBC还是像 Hibernate这样的对象关系映射（object-relational mapping，ORM）框架实现数据持久化，Spring都能够帮你消除持久化代码中那些单调枯燥的 数据访问逻辑。我们可以依赖Spring来处理底层的数据访问，这样就可以专注于应用程序中数据的管理了。

当开发Spittr应用的持久层的时候，会面临多种选择，我们可以使用JDBC、Hibernate、Java持久化API（Java Persistence API，JPA）或者其 他任意的持久化框架。你可能还会考虑使用最近很流行的NoSQL数据库（其实我更喜欢将其称为无模式数据库）。

幸好，不管你选择哪种持久化方式，Spring都能够提供支持。在本章，我们主要关注于Spring对JDBC的支持。但首先，我们来熟悉一下Spring 的持久化哲学，从而为后面打好基础。

### 1. Spring的数据访问哲学

为了避免持久化的逻辑分散到应用的各个组件中，最好将数据访问的功能放到一个或多个专注于此项任务的组件中。这样的组件通常称为数据访问对象（data access object，DAO）或Repository。

为了避免应用与特定的数据访问策略耦合在一起，编写良好的Repository应该以接口的方式暴露功能。

服务对象 --> Repository接口  -->  Repository实现

服务对象通过接口来访问Repository。这样做会有几个好处。
* 第一，它使得服务对象易于测试，因为它们不再与特定的数据访问实 现绑定在一起。实际上，你可以为这些数据访问接口创建mock实现，这样无需连接数据库就能测试服务对象，而且会显著提升单元测试的效 率并排除因数据不一致所造成的测试失败。
* 此外，数据访问层是以持久化技术无关的方式来进行访问的。持久化方式的选择独立于Repository，同时只有数据访问相关的方法才通过接口进行暴露。这可以实现灵活的设计，并且切换持久化框架对应用程序其他部分所带来的影响最小。如果将数据访问层的实现细节渗透到应用程序的其他部分中，那么整个应用程序将与数据访问层耦合在一起，从而导致僵化的设计。
* 接口与Spring：如果在阅读了上面几段文字之后，你能感受到我倾向于将持久层隐藏在接口之后，那很高兴我的目的达到了。我相信接口是实 现松耦合代码的关键，并且应将其用于应用程序的各个层，而不仅仅是持久化层。还要说明一点，尽管Spring鼓励使用接口，但这并不是强制的——你可以使用Spring将bean（DAO或其他类型）直接装配到另一个bean的某个属性中，而不需要一定通过接口注入。
* 为了将数据访问层与应用程序的其他部分隔离开来，Spring采用的方式之一就是提供统一的异常体系，这个异常体系用在了它支持的所有持久化方案中。

#### 1.1 了解Spring的数据访问异常体系

SQLException表 示在尝试访问数据库的时出现了问题，但是这个异常却没有告诉你哪里出错了以及如何进行处理.

可能导致抛出SQLException的常见问题包括：
* 应用程序无法连接数据库；
* 应用程序无法连接数据库；
* 要执行的查询存在语法错误；
* 查询中所使用的表和/或列不存在；
* 试图插入或更新的数据违反了数据库约束。

SQLException的问题在于捕获到它的时候该如何处理。事实上，能够触发SQLException的问题通常是不能在catch代码块中解决的。大多 数抛出SQLException的情况表明发生了致命性错误。如果应用程序不能连接到数据库，这通常意味着应用不能继续使用了。类似地，如果 查询时出现了错误，那在运行时基本上也是无能为力。

如果无法从SQLException中恢复，那为什么我们还要强制捕获它呢？

即使对某些SQLException有处理方案，我们还是要捕获SQLException并查看其属性才能获知问题根源的更多信息。这是因 为SQLException被视为处理数据访问所有问题的通用异常。对于所有的数据访问问题都会抛出SQLException，而不是对每种可能的问题 都会有不同的异常类型。

一些持久化框架提供了相对丰富的异常体系。例如，Hibernate提供了二十个左右的异常，分别对应于特定的数据访问问题。这样就可以针对想 处理的异常编写catch代码块。

即便如此，Hibernate的异常是其本身所特有的。正如前面所言，我们想将特定的持久化机制独立于数据访问层。如果抛出了Hibernate所特有 的异常，那我们对Hibernate的使用将会渗透到应用程序的其他部分。如果不这样做的话，我们就得捕获持久化平台的异常，然后将其作为平台 无关的异常再次抛出。

一方面，JDBC的异常体系过于简单了——实际上，它算不上一个体系。另一方面，Hibernate的异常体系是其本身所独有的。我们需要的数据 访问异常要具有描述性而且又与特定的持久化框架无关。

**Spring所提供的平台无关的持久化异常**

Spring JDBC提供的数据访问异常体系解决了以上的两个问题。不同于JDBC，Spring提供了多个数据访问异常，分别描述了它们抛出时所对 应的问题。表10.1对比了Spring的部分数据访问异常以及JDBC所提供的异常。

从表中可以看出，Spring为读取和写入数据库的几乎所有错误都提供了异常。Spring的数据访问异常要比表10.1所列的还要多。（在此没有列 出所有的异常，因为我不想让JDBC显得太寒酸。）



#### 1.2 数据访问模板化

Spring提供的数据访问模板
* `jca.cci.core.CciTemplate`	JCA CCI 连接
* `jdbc.core.JdbcTemplate`	JDBC 连接
* `jdbc.core.namedparam.NamedParameterJdbcTemplate` 支持命名参数的JDBC连接
* `jdbc.core.simple.SimpleJdbcTemplate`	通过Java 5简化后的JDBC连接 (Spring 3.1已废弃)
* `orm.hibernate3.HibernateTemplate`	Hibernate 3.x以上的sessions
* `orm.ibatis.SqlMapClientTemplate`	iBATIS SqlMap客户端
* `orm.jdo.JdoTemplate`	Java数据对象 (Data Object) 实现
* `orm.jpa.JpaTemplate`	Java持久化API的实体管理器


### 2. 数据源配置

无论选择Spring的哪种数据访问方式，你都需要配置一个数据源的引用。Spring提供了在Spring上下文中配置数据源bean的多种方式，包括：
* 通过JDBC驱动程序定义的数据源；
* 通过JNDI查找的数据源；
* 连接池的数据源。
*
对于即将发布到生产环境中的应用程序，我建议使用从连接池获取连接的数据源。如果可能的话，我倾向于通过应用服务器的JNDI来获取数据源。请记住这一点，让我们首先看一下如何配置Spring从JNDI中获取数据源。

#### 2.1 使用JNDI数据源

Spring应用程序经常部署在Java EE应用服务器中，如WebSphere、JBoss或甚至像Tomcat这样的Web容器中。这些服务器允许你配置通过JNDI获取数据源。这种配置的好处在于数据源完全可以在应用程序之外进行管理, 这样应用程序只需在访问数据库的时候查找数据源就可以了。另外，在应用服务器中管理的数据源通常以池的方式组织，从而具备更好的性能，并且还支持系统管理员对其进行热切换。

利用Spring，我们可以像使用Spring bean那样配置JNDI中数据源的引用并将其装配到需要的类中。如果应用程序的数据源配置在JNDI中，我们可以使用<jee:jndi-lookup>元素将其装配到Spring中:   
`<jee:jndi-lookup id="dataSource" jndi-name="/jdbc/SpitterDS" resource-ref="true" />`

或采用java配置  
```java
@Bean
public JndiObjectFactoryBean dataSource() {
  JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
  jndiObjectFB.setJndiName("jdbc/SpittrDS");
  jndiObjectFB.setResourceRef(true);
  jndiObjectFB.setProxyInterface(javax.sql.DataSource.class);
  return jndiObjectFB;
}
```


#### 2.2 使用数据源连接池

如果你不能从JNDI中查找数据源，那么下一个选择就是直接在Spring中配置数据源连接池。尽管Spring并没有提供数据源连接池实现，但是我 们有多项可用的方案，包括如下开源的实现：
* Apache Commons DBCP (http://jakarta.apache.org/commons/dbcp)；
* c3p0 (http://sourceforge.net/projects/c3p0/)；
* BoneCP (http://jolbox.com/) 。

这些连接池中的大多数都能配置为Spring的数据源，在一定程度上与Spring自带 的DriverManagerDataSource或SingleConnectionDataSource很类似（我们稍后会对其进行介绍）。例如，如下就是配置DBCP BasicDataSource的方式：
```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
  p:driverClassName="org.h2.Driver"
  p:url="jdbc:h2:tcp://localhost/~/spitter"
  p:username="sa"
  p:password=""
  p:initialSize="5"
  p:maxActive="10" />
```
java配置
```java
@Bean
public BasicDataSource dataSource() {
  BasicDataSource ds = new BasicDataSource();
  ds.setDriverClassName("org.h2.Driver");
  ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
  ds.setUsername("sa");
  ds.setPassword("");
  ds.setInitialSize(5);
  ds.setMaxActive(10);
  return ds;
}
```

前四个属性是配置BasicDataSource所必需的。属性driverClassName指定了JDBC驱动类的全限定类名。在这里我们配置的是H2数据库的数据源。属性url用于设置数据库的JDBC URL。最后，username和password用于在连接数据库时进行认证。

以上四个基本属性定义了BasicDataSource的连接信息。


#### 2.3 基于JDBC驱动的数据源

在Spring中，通过JDBC驱动定义数据源是最简单的配置方式。Spring提供了三个这样的数据源类（均位 于org.springframework.jdbc.datasource包中）供选择：
* DriverManagerDataSource：在每个连接请求时都会返回一个新建的连接。与DBCP的BasicDataSource不同， 由DriverManagerDataSource提供的连接并没有进行池化管理；
* SimpleDriverDataSource：与DriverManagerDataSource的工作方式类似，但是它直接使用JDBC驱动，来解决在特定环境下 的类加载问题，这样的环境包括OSGi容器；
* SingleConnectionDataSource：在每个连接请求时都会返回同一个的连接。尽管SingleConnectionDataSource不是严格意 义上的连接池数据源，但是你可以将其视为只有一个连接的池。

以上这些数据源的配置与DBCPBasicDataSource的配置类似。
```java
@Bean
public DataSource dataSource() {
  DriverManagerDataSource ds = new DriverManagerDataSource();
  ds.setDriverClassName("org.h2.Driver");
  ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
  ds.setUsername("sa");
  ds.setPassword("");
  return ds;
}
```

xml配置
```xml
<bean id="dataSource"
  class="org.springframework.jdbc.datasource.DriverManagerDataSource"
  p:driverClassName="org.h2.Driver"
  p:url="jdbc:h2:tcp://localhost/~/spitter"
  p:username="sa"
  p:password="" />
```

#### 2.4 使用嵌入式的数据源

除此之外，还有一个数据源是我想对读者介绍的：嵌入式数据库（embedded database）。嵌入式数据库作为应用的一部分运行，而不是应用连接的独立数据库服务器。尽管在生产环境的设置中，它并没有太大的用处，但是对于开发和测试来讲，嵌入式数据库都是很好的可选方案。 这是因为每次重启应用或运行测试的时候，都能够重新填充测试数据。

Spring的jdbc命名空间能够简化嵌入式数据库的配置。例如，如下的程序清单展现了如何使用jdbc命名空间来配置嵌入式的H2数据库，它会预先加载一组测试数据。

**使用jdbc命名空间配置嵌入式数据库**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/jdbc
      http://www.springframework.org/schema/jdbc/spring-jdbc-3.1.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
	<!-- ... -->
	<jdbc:embedded-database id="dataSource" type="H2">
		<jdbc:script location="com/habuma/spitter/db/jdbc/schema.sql"/>
		<jdbc:script location="com/habuma/spitter/db/jdbc/test-data.sql"/>
	</jdbc:embedded-database>
	<!-- ... -->
</beans>
```

java配置
```java
@Bean
public DataSource dataSource() {
  return new EmbeddedDatabaseBuilder()
      .setType(EmbeddedDatabaseType.H2)
      .addScript("classpath:schema.sql")
      .addScript("classpath:test-data.sql")
      .build();
}
```

```java
EmbeddedDatabaseBuilder builder = new EmbeddedDatabaseBuilder();
EmbeddedDatabase db = builder.setType(H2).addScript("schema.sql").addScript("data.sql").build();
db.shutdown();
```


#### 2.5 使用profile选择数据源

我们已经看到了多种在Spring中配置数据源的方法，我相信你已经找到了一两种适合你的应用程序的配置方式。实际上，我们很可能面临这样 一种需求，那就是在某种环境下需要其中一种数据源，而在另外的环境中需要不同的数据源。

例如，对于开发期来说，<jdbc:embedded-database>元素是很合适的，而在QA环境中，你可能希望使用DBCP 的BasicDataSource，在生产部署环境下，可能需要使用<jee:jndi-lookup>。

我们在第3章所讨论的Spring的bean profile特性恰好用在这里，所需要做的就是将每个数据源配置在不同的profile中，如下所示：




### 3. 在Spring中使用JDBC

#### 3.1 应对失控的JDBC代码

#### 3.2 使用JDBC模板

### 4. 总结






### 第十一章 使用对象-关系映射持久数据库
### 第十二章 使用NoSQL数据库
### 第十三章 缓存数据
### 第十四章 保护方法应用
