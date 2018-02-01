
## Spring

#### Spring 注解

参考文档
* Spring Annotations [Quick Reference](http://javabeat.net/spring-annotations/)
* [Annotations](http://www.oschina.net/uploads/doc/annotations/spring.html)
* [深入理解Java：注解 Annotation](http://www.cnblogs.com/peida/archive/2013/04/24/3036689.html)
* [Spring常用注解](http://www.cnblogs.com/xdp-gacl/p/3495887.html)

**常用注解**
* 标注组件： `@Component`、`@Service`、`@Controller`、`@Repository`
* Java装配： `@Configuration`
* 组件扫描： `@ComponentScan`
* 自动装配： `@Autowired`

### 装配bean
在Spring中装配bean有多种方式,三种主要的装配机制：
* 在XML中进行显式配置。
* 在Java中进行显式配置。
* 隐式的bean发现机制和自动装配。

如果不想在xml文件中配置bean，我们可以给我们的类加上spring组件注解，只需再配置下spring的扫描器就可以实现bean的自动载入。
> 1. 标注类 bean: `@Component`、`@Service`、`@Controller`、`@Repository`
> 2. 启动Spring自动组件扫描: 配置 `@ComponentScan` 或 xml配置 `<component-scan>`标签，
> 3. 自动装配: `@Autowired`

**@Component、@Service、@Controller、@Repository**

`@Component`、`@Service`、`@Controller`、`@Repository`用于将所标注的类加载到Spring环境中，需要搭配 `component-scan` 使用。
* `@Component` 泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。
* `@Service` 用于标注业务层组件
* `@Controller` 用于标注控制层组件（如struts中的action）
* `@Repository` 用于标注数据访问组件，即DAO组件

**@ComponentScan**
* `@ComponentScan` 启用了组件扫描。默认会扫描与配置类相同的包。
* 等同xml配置文件 `<context:component-scan>` 标签。
* 默认情况下自动扫描指定路径下的包（含所有子包），将带有`@Component`、`@Repository`、`@Service`、`@Controller`标签的类自动注册到spring容器。
* 对标记了`@Required`、`@Autowired`、`@PostConstruct`、`@PreDestroy`、`@Resource`、`@WebServiceRef`、`@EJB`、`@PersistenceContext`、`@PersistenceUnit`等注解的类进行对应的操作使注解生效（包含了annotation-config标签的作用）。

**@Autowired**  
* `@Autowired` 可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。[示例](http://blog.csdn.net/HEYUTAO007/article/details/5981555)  
* 默认按类型装配，默认情况下必须要求依赖对象必须存在。

**@Configuration、@Bean**
* `@Configuration`注解表明这个类是一个配置类。
* `@Configuration` 是 Spring 3.X 后提供的注解，用于配置 Spring，可取代XML配置方式。
* `@configuration`注解能与XML配置方式结合使用。
* `@Bean`标注方法等价于XML中配置bean。

**@ContextConfiguration**
* 用来指定加载的Spring配置文件的位置,会加载默认配置文件。
* `@ContextConfiguration` 注解有以下两个常用的属性：`locations` 和 `inheritLocations`
* `locations`：可以通过该属性手工指定 Spring 配置文件所在的位置，可以指定一个或多个 Spring 配置文件。
* `inheritLocations`：是否要继承父测试用例类中的 Spring 配置文件，默认为 true。


#### XML配置
典型的spring配置文件 (application-config.xml)
````xml
<beans>
	<bean id="orderService" class="com.acme.OrderService"/>
		<constructor-arg ref="orderRepository"/>
	</bean>
	<bean id="orderRepository" class="com.acme.OrderRepository"/>
		<constructor-arg ref="dataSource"/>
	</bean>  
</beans> 
````
Java代码
````java
ApplicationContext ctx = new ClassPathXmlApplicationContext("application-config.xml");  
OrderService orderService = (OrderService) ctx.getBean("orderService");  
````

#### Java配置
`AnnotationConfigApplicationContext` 搭配上 `@Configuration` 和 `@Bean` 注解
Java 配置
````java
@Configuration  
public class ApplicationConfig {
	@Bean
	public OrderService orderService() {
		return new OrderService(orderRepository());
	}
	@Bean
	public OrderRepository orderRepository() {
		return new OrderRepository(dataSource());
	}
}  
````
Java代码
````java
JavaConfigApplicationContext ctx = 
	new JavaConfigApplicationContext(ApplicationConfig.class);  
OrderService orderService = ctx.getBean(OrderService.class);  
````


----
### 依赖注入与配置示例 (XML/Java)

````
Knight.java                 # Knight接口
BraveKnight.java            # Knight接口实现(构造器注入 Quest)
Quest.java                  # Quest接口
RescueDamselQuest.java      # Quest接口实现
SlayDragonQuest.java        # Quest接口实现()
Minstrel.java               # Minstrel类(AOP)
KnightConfig.java           # 基于Java的配置
KnightMain.java             # 加载包含Knight的Spring上下文(knight.xml/minstrel.xml)
KnightsApp.java`            # 使用KnightConfig配置 (Java Config)
````

`Quest.java `
````java
// Quest接口
public interface Quest {
    void embark();
}
````

`Knight.java `
````java
//  Knight接口
public interface Knight {
    void embarkOnQuest();
}
````

`BraveKnight.java` 实现 Knight 接口
````java
public class BraveKnight implements Knight {
    private Quest quest;

    public BraveKnight(Quest quest) {
        this.quest = quest;
    }

    public void embarkOnQuest() {
        quest.embark();
    }
}
````

`SlayDragonQuest.java` 实现 Quest 接口
````java
public class SlayDragonQuest implements Quest {
	private PrintStream stream;

	public SlayDragonQuest(PrintStream stream) {
		this.stream = stream;
	}

	public void embark() {
		stream.println("Embarking on quest to slay the dragon!");
	}

}
````

`RescueDamselQuest.java` 实现 Quest 接口
````java
public class RescueDamselQuest implements Quest {

    public void embark() {
        System.out.println("Embarking on a quest to rescue the damsel.");
    }

}
````

`KnightConfig.java` Java Configuration (注入依赖)
````java
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
````

`knight.xml`XML配置 (注入依赖)
````xml
<bean id="knight" class="sia.knights.BraveKnight">
  <constructor-arg ref="quest" />
</bean>

<bean id="quest" class="sia.knights.SlayDragonQuest">
  <constructor-arg value="#{T(System).out}" />
</bean>
````

`KnightMain.java` 使用XML配置
````java
ClassPathXmlApplicationContext context =
	new ClassPathXmlApplicationContext("META-INF/spring/knight.xml");
	//new ClassPathXmlApplicationContext("META-INF/spring/minstrel.xml");

Knight knight = context.getBean(Knight.class);
knight.embarkOnQuest();
````

`KnightsApp.java` 使用KnightConfig配置 (Java Config)
````java
AnnotationConfigApplicationContext context =
    new AnnotationConfigApplicationContext(KnightConfig.class);

Knight knight = context.getBean(Knight.class);
knight.embarkOnQuest();
````
**切面 AOP**

`Minstrel.java` 切面
````java
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
````

`minstrel.xml` XML配置
````xml
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
````

### 隐式的bean发现机制和自动装配 
````
Knight.java                 # 不变
BraveKnight.java            # @Component 与 @Autowired
Quest.java                  # 不变
RescueDamselQuest.java      # @Component 与 @Autowired
SlayDragonQuest.java        # @Component 与 @Autowired
Minstrel.java               # 不变
KnightConfig.java           # @ComponentScan
KnightMain.java             # 不变
KnightsApp.java`            # 不变
````

`BraveKnight.java` (增加`@Component` 与 `@Autowired`)
````java
@Component
public class BraveKnight implements Knight {
    private Quest quest;

	@Autowired
    public BraveKnight(Quest quest) {
        this.quest = quest;
    }

    public void embarkOnQuest() {
        quest.embark();
    }
}
````

`SlayDragonQuest.java` (增加`@Component` 与 `@Autowired`)
````java
@Component
public class SlayDragonQuest implements Quest {
	private PrintStream stream;

	public SlayDragonQuest(PrintStream stream) {
		this.stream = stream;
	}

	public void embark() {
		stream.println("Embarking on quest to slay the dragon!");
	}

}
````

`RescueDamselQuest.java` (增加 `@Component`)
````java
@Component
public class RescueDamselQuest implements Quest {

    public void embark() {
        System.out.println("Embarking on a quest to rescue the damsel.");
    }

}
````

`KnightConfig.java` (`@ComponentScan` 启用组件扫描)
````java
@Configuration
@ComponentScan
public class KnightConfig {
}
````

`SlayDragonQuest` 与 `RescueDamselQuest` 有歧义，`@Primary` 消除歧义
````java
@Configuration
@ComponentScan
public class KnightConfig {
    @Bean
    @Primary
    public Quest slayDragonQuest() {
        return new SlayDragonQuest(System.out);
    }
}
````

`knight.xml` XML配置 (`component-scan` 启动组件扫描)
````xml
<context:component-scan base-package="sia.knights2" />
````



----

### Beans, BeanFactory和ApplicationContext

**参考资料**
* [ApplicationContext与容器初始化](http://www.2cto.com/kf/201605/511658.html)
* [Spring的三大核心接口——BeanFactory、ApplicationContext、WebApplicationContext](http://www.th7.cn/Program/java/201701/1093708.shtml)
*


BeanFactory实际上是实例化，配置和管理众多bean的容器。

BeanFactory本质上不过是高级工厂的接口，它维护不同bean和它们所依赖的bean的注册。 BeanFactory使得你可以利用 bean工厂读取和访问bean定义。


#### ApplicationContext

beans包提供了以编程的方式管理和操控bean的基本功能，而context包增加了ApplicationContext，它以一种更加面向框架的方式增强了BeanFactory的功能。多数用户可以以一种完全的声明式方式来使用ApplicationContext，甚至不用去手动创建它，但是却去依赖象ContextLoader的支持类，在J2EE的的web应用的启动进程中用它启动ApplicationContext。当然，这种情况下还是可以以编程的方式创建一个ApplicationContext。
Context包的基础是位于org.springframework.context包中的ApplicationContext接口。它是由BeanFactory接口集成而来，提供BeanFactory所有的功能。为了以一种更向面向框架的方式工作，context包使用分层和有继承关系的上下文类，包括：
MessageSource, 提供对i18n消息的访问
资源访问, 比如URL和文件
事件传递给实现了ApplicationListener接口的bean
载入多个（有继承关系）上下文类，使得每一个上下文类都专注于一个特定的层次，比如应用的web层
因为ApplicationContext包括了BeanFactory所有的功能，所以通常建议先于BeanFactory使用，除了有限的一些场合比如在一个Applet中，内存的消耗是关键的，每kb字节都很重要。接下来的章节将叙述ApplicationContext在BeanFactory的基本能力上增建的功能。



spring容器最基本的接口是BeanFactory,他负责配置、创建、管理bean，他的子接口之一：ApplicationContext,也叫做spring的上下文。ApplicationContext是BeanFactory的子接口，在web应用中，通常会用到XmlWebApplicationContext、AnnotationCofigWebApplicationContext两个实现类。

ApplicationContext简介

系统创建ApplicationContext容器的时候，默认会预先初始化所有的单例的bean，调用构造器创建实例对象，然后通过set方法注入依赖的对象实例。这样的情况下，也就是说会面临着一个问题，容器在初始化的时候会有较大的性能的消耗，但是一旦初始化完成之后，程序在获取单例的bean的时候，又会获得较好的性能。
