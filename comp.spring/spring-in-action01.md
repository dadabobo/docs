## 第一部分 Spring的核心

Spring的主要特性为注入依赖（DI）和面向切面编程（AOP）。

学习Spring容器、依赖注入（dependency injection，DI）和面向切面编程（aspect-oriented programming，AOP），也就是Spring框架的核心。理解Spring的基础原理，而这些原理将会在本书各个章节都会用到。

## 第一章 Spring之旅

介绍Spring的注入依赖（DI）和面向切面编程（AOP）。

### 1. 简化Java 开发

Spring提供了更加轻量级和简单的编程模型。  
Spring竭力避免因自身的API而弄乱你的应用代码。Spring不会强迫你实现Spring规范的接口或继承Spring规范的类。  

为了降低Java开发的复杂性，Spring采取了以下4种关键策略：
* 基于POJO的轻量级和最小侵入性编程；
* 通过依赖注入和面向接口实现松耦合；
* 基于切面和惯例进行声明式编程；
* 通过切面和模板减少样板式代码。

Spring竭力避免因自身的API而弄乱你的应用代码。Spring不会强迫你实现Spring规范的接口或继承Spring规范的类。最坏的场景是，一个类或许会使用Spring注解，但它依旧是POJO。

#### 注入依赖
`DamselRescuingKnight` 与 `RescueDamselQuest` 紧耦合  
`DamselRescuingKnight.java`
```java
public class DamselRescuingKnight implements Knight {
    private RescueDamselQuest quest;

    public DamselRescuingKnight() {
        this.quest = new RescueDamselQuest();
    }

    public void embarkOnQuest() {
        quest.embark();
    }
}
```

`BraveKnight` 没有与任何特定的 `Quest` 实现发生耦合。
只要实现了`Quest`接口，具体是哪种类型`Quest`无关紧要。这就是DI所带来的最大收益——松耦合。如果一个对象只通过接口（而不是具体实现或初始化过程）来表明依赖关系，那么这种依赖就能够在对象本身毫不知情的情况下，用不同的具体实现进行替换。  
`Quest.java` 与 `Knight.java`
```java
// Quest接口
public interface Quest {
    void embark();
}

// Knight接口
public interface Knight {
    void embarkOnQuest();
}
```

`BraveKnight`没有自行创建探险任务，而是在构造的时候把探险任务作为构造器参数传入(**构造器注入**)。  
`BraveKnight.java`
```java
public class BraveKnight implements Knight {
    private Quest quest;

    public BraveKnight(Quest quest) {
        this.quest = quest;
    }

    public void embarkOnQuest() {
        quest.embark();
    }
}
```

`SlayDragonQuest` 实现 `Quest` 接口, 它适合注入到BraveKnight中去。  
`SlayDragonQuest.java`
```java
public class SlayDragonQuest implements Quest {
    private PrintStream stream;

    public SlayDragonQuest(PrintStream stream) {
        this.stream = stream;
    }

    public void embark() {
        stream.println("Embarking on quest to slay the dragon!");
    }

}
```

如何将`SlayDragonQuest`交给`BraveKnight`呢？又如何将`PrintStream`交给`SlayDragonQuest`呢？

创建应用组件之间协作的行为通常称为 **装配(wiring)**。Spring有多种装配bean的方式，采用XML是很常见的一种装配方式。
如果XML配置不符合你的喜好的话，Spring还支持使用Java来描述配置。

使用Spring将`SlayDragonQuest`注入到`BraveKnight`中。(`knights.xml`)
```xml
<beans>

  <bean id="knight" class="sia.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="sia.knights.SlayDragonQuest">
    <constructor-arg value="#{T(System).out}" />
  </bean>

</beans>
```

Spring提供了基于Java的配置，可作为XML的替代方案. (`KnightConfig.java`)
```java
@Configuration
public class KnightConfig {

  @Bean
  public Knight knight() {
    return new BraveKnight(quest());
  }

  @Bean
  public Quest quest() {
    return new SlayDragonQuest(System.out);
  }

}
```

Spring通过 **应用上下文(Application Context)** 装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。

这里的`main()`方法基于`knights.xml`文件创建了Spring应用上下文。随后它调用该应用上下文获取一个ID为`knight`的bean。得到Knight对象的 引用后，只需简单调用`embarkOnQuest()`方法就可以执行所赋予的探险任务了。  

`KnightMain.java`
```java
public class KnightMain {

  public static void main(String[] args) throws Exception {
    ClassPathXmlApplicationContext context =
        new ClassPathXmlApplicationContext("META-INF/spring/knight.xml");
    Knight knight = context.getBean(Knight.class);
    knight.embarkOnQuest();
    context.close();
  }

}
```
`KnightsApp.java`
```java
public class KnightsApp {

  public static void main(String[] args) {
    AnnotationConfigApplicationContext context =
        new AnnotationConfigApplicationContext(KnightConfig.class);

    Knight knight = context.getBean(Knight.class);
    knight.embarkOnQuest();
    context.close();
  }
}
```


#### 应用切面
DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来。

面向切面编程往往被定义为促使软件系统实现关注点的分离一项技术。系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实 现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的 组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。  

`Minstrel.java`
```java
public class Minstrel {

  private PrintStream stream;

  public Minstrel(PrintStream stream) {
    this.stream = stream;
  }

  public void singBeforeQuest() {
    stream.println("Fa la la, the knight is so brave!");
  }

  public void singAfterQuest() {
    stream.println("Tee hee hee, the brave knight " +
    		"did embark on a quest!");
  }

}
```

将`Minstrel`声明为一个切面 (`minstrel.xml`)

```xml
<beans >

  <bean id="knight" class="sia.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="sia.knights.SlayDragonQuest">
    <constructor-arg value="#{T(System).out}" />
  </bean>

  <bean id="minstrel" class="sia.knights.Minstrel">
    <constructor-arg value="#{T(System).out}" />
  </bean>

  <aop:config>
    <aop:aspect ref="minstrel">
      <aop:pointcut id="embark"
          expression="execution(* *.embarkOnQuest(..))"/>

      <aop:before pointcut-ref="embark"
          method="singBeforeQuest"/>

      <aop:after pointcut-ref="embark"
          method="singAfterQuest"/>
    </aop:aspect>
  </aop:config>

</beans>
```

使用了Spring的`aop`配置命名空间把`Minstrel` bean声明为一个切面。

* 首先，需要把Minstrel声明为一个bean
* 然后在`<aop:aspect>`元素中引用该bean。
* 切入点是在前边的`<pointcut>`元素中定义，并配置expression属性来选择所应用的通知。表达式的语法采用的是AspectJ的切点表达式语言。
* 为了进一步定义切面，声明（使用`<aop:before>`）在`embarkOnQuest()`方法执行前调用`Minstrel`的`singBeforeQuest()`方法。
* `pointcut-ref`属性都引用了名字为`embank`的切入点。


```xml
<aop:pointcut id="embark"
    expression="execution(* *.embarkOnQuest(..))"/>

<aop:before pointcut-ref="embark"
    method="singBeforeQuest"/>
```

#### 使用模板消除样板式代码
Spring旨在通过模板封装来消除样板式代码。Spring的`JdbcTemplate`使得执行数据库操作时，避免传统的JDBC样板代码成为了可能。


### 2. 容纳你的Bean

在基于Spring的应用中，你的应用对象生存于Spring容器（container）中。Spring容器负责创建对象，装配它们，配置它们并管 理它们的整个生命周期，从生存到死亡。

#### 使用应用上下文

Spring自带了多种类型的应用上下文。下述为常见的：
* `AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。
* `AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
* `ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类 资源。
* `FileSystemXmlapplicationcontext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。
* `XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。

#### bean的的生命周期
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


### 3. 俯瞰Spring风景线
#### Spring模块
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

#### Spring Portfolio
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

### 4. Spring的新功能
略。
