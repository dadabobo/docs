
##Java


http://outofmemory.cn/code-snippet/3670/spring-inject-by-annotation

http://www.cnblogs.com/nerxious/archive/2013/01/25/2876489.html
http://blog.csdn.net/zhikun518/article/details/7185499
http://outofmemory.cn/code-snippet/3670/spring-inject-by-annotation
http://www.cnblogs.com/forerver-elf/p/4724199.html
http://blog.csdn.net/woshixuye/article/details/7700455
http://blog.csdn.net/luckyjda/article/details/9104045
https://yq.aliyun.com/articles/19641
http://www.cnblogs.com/forerver-elf/p/4724205.html
http://www.cnblogs.com/forerver-elf/p/4724211.html
http://www.cnblogs.com/nerxious/archive/2013/01/25/2876489.html
http://www.cnblogs.com/nerxious/archive/2012/12/24/2829446.html

[Using JConsole](https://docs.oracle.com/javase/8/docs/technotes/guides/management/jconsole.html)
[jconsole工具使用](http://www.cnblogs.com/kongzhongqijing/articles/3621441.html)
[JConsole的使用手册](http://www.open-open.com/lib/view/open1345646982251.html)

#### Spring依赖注入：注解注入总结

注解注入顾名思义就是通过注解来实现注入，Spring和注入相关的常见注解有`Autowired`、`Resource`、`Qualifier`、`Service`、`Controller`、`Repository`、`Component`。
* `Autowired`是自动注入，自动从spring的上下文找到合适的bean来注入
* `Resource`用来指定名称注入
* `Qualifier`和`Autowired`配合使用，指定bean的名称
* `Service`，`Controller`，`Repository`分别标记类是Service层类，Controller层类，数据存储层的类，spring扫描注解配置时，会标记这些类要生成bean。
* `Component`是一种泛指，标记类是组件，spring扫描注解配置时，会标记这些类要生成bean。

上面的Autowired和Resource是用来修饰字段，构造函数，或者设置方法，并做注入的。而`Service`，`Controller`，`Repository`，`Component`则是用来修饰类，标记这些类要生成bean。

下面我们通过实例项目来看下spring注解注入的使用。

首先新建一个maven项目，并在pom中添加spring相关的依赖，如果不知道添加那些依赖，请参照上一篇文章。

然后新建`CarDao`类，给它添加`@Repository`注解，如下代码：
```java
package cn.outofmemory.helloannotation;

import org.springframework.stereotype.Repository;

@Repository
public class CarDao {
    public void insertCar(String car) {
        String insertMsg = String.format("inserting car %s", car);
        System.out.println(insertMsg);
    }

}
```

新建`CarServic`e类，并给该类标注`@Service`注解，在这个类中定义`CarDao`的字段，并通过`Autowired`来修饰此字段，这样上面定义的`CarDao`类的实例就会自动注入到`CarService`的实例中了。

```java
package cn.outofmemory.helloannotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class CarService {

    @Autowired
    private CarDao carDao;

    public void addCar(String car) {
        this.carDao.insertCar(car);
    }
}
```

注意：`Autowired`注解有一个可以为空的属性`required`，可以用来指定字段是否是必须的，如果是必需的，则在找不到合适的实例注入时会抛出异常。

下面我们在App.java中使用上面测试下注解注入：
```java
package cn.outofmemory.helloannotation;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

/**
 * Hello world!
 *
 */
public class App
{
    public static void main( String[] args )
    {
        ApplicationContext appContext = new AnnotationConfigApplicationContext("cn.outofmemory.helloannotation");
        CarService service = appContext.getBean(CarService.class);
        service.addCar("宝马");
    }
}
```

在上面的main方法中首先我们初始化了`appContext`，他是`AnnotationConfigApplicationContext`，它的构造函数接受一个package的名称，来限定要扫描的package。然后就可以通过`appContext`的`getBean`方法获得`CarService`的实例了。

上面的例子非常简单，单纯的使用`AnnotationConfigApplicationContext`就可以了，但是在实际项目中情况往往没有这么简单，还是需要spring配置文件的。在spring配置文件中也可以通过下面的配置让spring自动扫描注解配置。

```xml
<!-- bean annotation driven -->
<context:annotation-config />
<context:component-scan base-package="cn.outofmemory.helloannotation" >
</context:component-scan>
```

下面我们看下混合使用spring配置和注解的例子，首先在项目中添加source folder，`src/main/resources`,并添加`spring.xml`, 其内容如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xsi:schemaLocation="http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
 http://www.springframework.org/schema/context
 http://www.springframework.org/schema/context/spring-context-3.0.xsd ">

    <!-- bean annotation driven -->
    <context:annotation-config />
    <context:component-scan base-package="cn.outofmemory.helloannotation" >
    </context:component-scan>

    <bean id="sqliteCarDao" class="cn.outofmemory.helloannotation.CarDao" >
        <constructor-arg name="driver" value="sqlite"/>
    </bean>
 </beans>
```

在上面的配置文件中，我们通过`context:annotation-config`和`context:component-sacn`节点来指定要扫描注解注入，然后又定义了一个id为`sqliteCarDao`的bean，它的构造函数的`driver`值为`sqlite`。

我们修改下App.java使用xml配置文件，再运行下App看下会怎样。
```java
package cn.outofmemory.helloannotation;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * Hello world!
 *
 */
public class App
{
    public static void main( String[] args )
    {
        //ApplicationContext appContext = new AnnotationConfigApplicationContext("cn.outofmemory.helloannotation");

        ApplicationContext appContext = new ClassPathXmlApplicationContext("/spring.xml");
        CarService service = appContext.getBean(CarService.class);
        service.addCar("宝马");
    }
}
```

运行程序发现输出为：inserting car 宝马 into mysql，显然`CarService`自动注入的`CarDao`使用了默认构造函数构造的实例。是否可以通过注解指定使用`spring.xml`中配置的`sqliteCarDao`呢？

我们可以修改下`CarService`类，通过`Qualifier`注解来指定要使用的bean的名字。

如下,在指定`Autowired`注解时，同时指定`Qualifier`注解指定bean的名字

```java
@Autowired
@Qualifier("sqliteCarDao")
private CarDao carDao;
```

重新运行下App.java 这次输出的是inserting car 宝马 into sqlite,这次使用了spring.xml中配置的bean了。

在文中开头我们还提到了Resouce注解，这个注解可以指定名字注入，我们再次修改下CarService类：

```java
@Resource(name="sqliteCarDao")
private CarDao carDao;
```

javax.annotation.Resource注解实现的效果和@Autowired+@Qualifier的效果是一样的。


[Spring注解注入的源码下载](http://outofmemory.cn/ugc/upload/00/20/20130617/hellospring-annotation.rar)
---

### Spring反射机制

Spring是分层的Java SE/EE应用一站式的轻量级开源框架，以IoC(Inverse of Control)和AOP(Aspect Oriented Programming)为内核，提供了展现层Spring MVC和持久层Spring JDBC以及业务层事务管理等众多的企业级应用技术，此外，Spring整合了开源世界里众多的第三方框架和类库。




**Spring的体系结构：**

Spring整个框架按其所属功能可划分为5个主要模块：数据访问和集成、Web及远程操作、测试框架、AOP和IoC。 
* **IoC**：Spring的核心模块实现了IoC的功能，它将类和类之间的依赖从代码中脱离出来，用配置的方式进行依赖关系描述，由IoC容器负责依赖类之间的创建、拼接、管理、获取等工作。`BeanFactory`接口是Spring框架的核心接口，它实现了容器许多核心的功能。`Context`模块构建于核心模块之上，扩展了`BeanFactory`的功能，添加了i18n国际化、Bean生命周期控制、框架事件体系、资源加载透明化等多项功能。此外，该模块还提供了许多企业级服务的支持。`ApplicationContext`是`Context`模块的核心接口。表达式语言模块是统一表达式语言的一个扩展，该表达式语言用于查询和管理运行期的对象，支持设置或获取对象属性，调用对象方法、操作数组、集合等。还提供了逻辑表达式运算、变量定义等功能。使用它可以方便地通过表达式串和Spring IoC容器进行交互。 
* **AOP模块**：AOP是进行横切逻辑编程的思想，开拓了人们考虑问题的思路。在AOP模块里，Spring提供了满足AOP Alliance规范的实现，此外，还整合了AspectJ这种AOP语言级的框架。Java 5.0引入`java.lang.instrument`，允许在JVM启动时启用一个代理类，通过该代理类在运行期修改类的字节码，改变一个类的功能，实现AOP的功能。 
数据访问和集成：Spring站在DAO的抽象层面，建立了一套DAO层统一的异常体系，同时将各种访问数据的检查型异常转换成非检查型异常，为整个各种持久层框架提供基础。其次，Spring通过模版化技术对各种数据访问技术进行了薄层的封装，将模式化的代码隐藏起来，使数据访问的程序得到了大幅简化。
* **Web及远程调用**：该模块建立在`ApplicationContext`模块之上，提供了Web应用的各种工具类，若通过`Listener`或`Servlet`初始化Spring容器，将Spring容器注册到Web容器中。其次，该模块还提供了多项面向Web的功能。此外，Spring还可以整合Struts、WebWork、Tapestry Web等MVC框架。 
* **Spring注解**：  
  在`ApplicationContext`文件中，使用Spring的`<context:component-scan base-package="">`扫描指定类包下的所有类，这样在类中定的Spring注解才能产生作用。   
  * `@Repository`：定义一个DAO Bean
  * `@Autowired`：将Spring容器中的Bean注入进来 
  * `@Service`：将一个类标注为一个服务层的Bean
  * `@ContextConfiguration`：指定Spring的配置容器
  * `@Controller`：将一个类标注为Spring MVC的`Controller`
  * `@RequestMapping(value="/index.html")`：负责处理一个`xxx.html`请求 
* **IoC**  
  DI(Dependency Injection)：让调用类的某一接口实现类的依赖关系由第三方(容器或协作类)注入，以移出调用类对某一接口实现类的的依赖。  
  从注入方法上来看，主要可以划分为3种类型：构造函数注入、属性注入和接口注入。Spring支持构造函数注入和属性注入。 
  * 构造函数注入：在构造函数注入中，我们通过调用类的构造函数，将接口实现类通过构造函数变量传入。
  * 属性注入：属性注入可以有选择地通过Setter方法完成调用类所需依赖的注入。 
  * 接口注入：将调用累所有依赖注入的方法抽取到一个接口中，调用类通过实现该接口提供相应的注入方法。 


**java的反射机制**

Java语言允许通过程序化的方式间接对Class的对象实例操作，Class文件由类装载器装载后，在JVM中将形成一份描述Class结构的元信息对象，通过该元信息对象可以获知Class的结构信息：构造函数、属性和方法等。Java允许用户借由这个Class相关的元信息对象间接调用Class对象的功能. 

例：

```java 
public Class Car{ 
   private String brand; 
   private String color; 
   private int maxSpeed; 
   
   public Car(){} 
   
   public Car(String brand,String color,int maxSpeed){ 
      this.brand = brand; 
      this.color = color; 
      this.maxSpeed = maxSpeed; 
   } 
  
   public void introduce(){ 
      System.out.println("brand"+brand+",color"+color+",+maxSpeed"+maxSpeed); 
   } 
   //... 
} 

import java.lang.reflect.Construcor; 
import java.lang.reflect.Field; 
import java.lang.reflect.Method; 

public class ReflectTest{ 
   public static Car initByDefaultConst() throws Throwable{ 
       //通过类加载器获取Car类对象 
       ClassLoader loader = Thread.currentThread().getContextClassLoader(); 
       Class clazz = loader.loadClass(Car): 
   
       //获取类的默认构造器对象并通过它实例化Car 
       Constructor cons = clazz.getDeclardConstructor((Class[])null); 
       Car car = (Car)cons.newInstance(): 

       //通过反射方法设置属性 
       Method setBrand = clazz.getMethod("setBrand",String.class); 
       setBrand.invoke(car,"WCA72"); 
       Method setColor = clazz.getMethod("setColor ",String.class); 
       setColor.invoke(car,"black"); 
       Method setMaxSpeed = clazz.getMethod("setMaxSpeed ",int.class); 
       setMaxSpeed.invoke(car,200); 
     
       return car; 
   } 

   public static void main(String[] args) throws Throwable{ 
       Car car = initByDefaultConst(); 
       car.introduce(); 
   } 
} 
```

**类装载器ClassLoader**

工作机制： 

类装载器就是寻找类的字节码文件并构造出类在JVM内部表示的对象组件。在Java中，类装载器装入JVM中，要经过以下步骤： 
1.装载：查找和导入Class文件 
2.链接：执行校验、准备和解析步骤，其中解析步骤是可以选择的： 
校验：检查载入Class文件数据的正确性 
准备：给类的静态变量分配存储空间 
解析：将符号引用转成直接引用 
3.初始化：对类的静态变量、静态代码块执行初始化工作 
类加载器工作由ClassLoader及其子类负责，ClassLoader是一个重要的Java运行时系统组件，它负责在运行时查找和装入Class字节码文件。JVM在运行时会产生三个ClassLoadre：根装载器、ExtClassLoader和AppClassLoader。根装载器不是ClassLoader的子类，负责装载JRE的核心类库。ExtClassLoader和AppClassLoader都是ClassLoader的子类。其中，EctClassLoader负责装载JRE扩展目录ext中的JAR类包，AppClassLoader负责装载Classpath路径下的类包。 

JVM装载类时使用“全盘负责委托机制”，“全盘负责”是指当一个ClassLoader装载一个类时，除非显式地使用另一个ClassLoader，该类所依赖及引用的类也由这个ClassLoader载入：“委托机制”是指先委托父装载器寻找目标类，只有在找不到的情况下才从自己的类路径中查找并装载目标类。 
ClassLoader的重要方法： 
Class loadClass(String name)：name参数指定类装载器需要装载类的名字，必须使用全限定类名。该方法有一个重载方法loadClass(String name，boolean resolve)，resolve参数告诉类装载器是否需要解析该类。在初始化类之前，应考虑进行类解析的工作，但并不是所有的类都需要解析，若JVM值需知道该类是否存在或找出该类的超类，那么就不需要进行解析。 
Class defineClass(String name,byte[] b,int off,int len)：将类文件的字节数组转换成JVM内部的java.lang.Class对象。字节数组可以从本地文件系统、远程网络获取。name为字节数组对应的全限定类名。 
Class findSystemClass(String name)：从本地文件系统载入Class文件，若本地文件系统更不存在该Class文件，将抛出ClassNotFoundException异常。 
Class findLoadedClass()：调用该方法来查看ClassLoader是否已装入某个类。若已装入，则返回java.lang.Class对象，否则返回null。 
ClassLoader getParent()：获取类装载器的父装载器。 

反射对象类在java.reflect包中定义，下面是最主要的三个反射类：
* **Constructor**：类的构造函数反射类，通过Class#getContructors()方法可以获得类的所有构造函数反射对象数组。在JDK 5.0中，还可以通过getContructor(Class parameterTypes)获取拥有特定入参的构造函数反射对象。Constructor的一个主要方法是newInstance(Object[] initargs)，通过该方法可以创建一个对象类的实例，相当于new关键字。 
* **Method**：类方法的反射类，通过Class#getDeclaredMehtods()方法可以获取类的所有方法反射类对象数组Method[]。在 JDK 5.0中可以通过getDeclaredMehtods(String name,Class parameterTypes)获取特定签名的方法，name为方法名；Class为方法入参类型列表。Method最主要的方法是invoke(Object obj,Object[] args)，obj表示操作的目标对象，args为方法入参。 
  Method还有很多用于获取类方法更多信息的方法： 
  * Class getReturnType()：获取方法的返回值类型
  * Class[] getParameterTypes()：获取方法的入参类型数组
  * Class[] getExceptionTypes()：获取方法的一场类型数组
  * Annotationp[][] getParamerterAnnotations()：获取方法的注解信息 
* **Field**：类的成员变量的反射类，通过Class#getDeclaredFields()方法可以获取类的成员变量反射对象数组，通过Class#getDeclaredFields(String name)则可获取某个特定名称的成员变量反射对象。Field类最主要的方法是set(Object obj,Object value)，obj表示操作目标评对象，通过value为目标对象的成员变量设置值。若成员变为为基础类型，用户可以使用Field类中提供的带类型名的值设置方法。 

通过反射机制可以调用私有变量和私有方法。但在访问private、protected成员变量和方法时必须通过setAccessible(boolean acess)方法取消java语言检查，否则抛出IllegalAccessException。若JVM的安全管理器设置了相应的安全机制，调用该方法将抛出SecurityException。 

Spring设计了一个Resource接口，它为应用提供了更强的访问底层资源的能力。该接口拥有对应不同资源类型的实现类。Resource接口的主要方法：
* boolean exists()：资源是否存在
* boolean isOpen()：资源是否打开
* URL getURL() throws IOException：若底层资源可以表示成URL，该方法返回对应的URL对象 
* File getFile() throws IOException：若底层资源对应一个文件，该方法返回对应的File对象 
* InputStream getInputStream() throws IOException：返回资源对应的输入流
 
Spring框架使用Resource装载各种资源，Resource的具体实现类如下： 
* ByteArrayResource：二进制数组表示的资源，二进制数组资源可以在内存中通过程序构造
* ClassPathResource：类路径下的资源，资源以相对于类路径的方式表示 
* FileSystemResource：文件系统资源，资源以文件系统路径的方式表示
* InputStreamResource：对应一个InputStream的资源 
* ServletContextResource：为访问web容器上下文中的资源而设计的类，负责以相对于Web应用根目录的路径加载资源，它支持已流和URL的方式访问，在WAR解包的情况下，也可以通过File的方式访问，还可以直接从JAR包中访问资源。 
* UrlResource：封装了java.net.URL，它使用户能够访问任何可以通过URL表示的资源。 

对资源进行编码： 
EncodedResource encRes = new EncodedResource(res,"UTF-8"); 

                         资源类型的地址前缀   
地址前缀             示例                       
classpath      classpath:com/beans.xml   
对应资源类型：从类路径中加载资源，classpath:和classpath:/是等价的，都是相当于类的跟路径。资源文件可以在标准的文件系统中，也可以在jar或zip的类包中 
file:         file:/com/beans.xml 
对应资源类型：使用UrlResource从文件系统目录中装载资源，可采用绝对或相对路径 
http://       http://www.beans.xml 
对应资源类型：使用UrlResource从Web服务器中装载资源 
ftp://        ftp://www.beans.xml 
对应资源类型：使用UrlResource从FTP服务器中装载资源 
没有前缀      com/beans.xml 
对应资源类型：根据ApplicationContext具体实现类采用对应的类型的Resource 

Ant风格资源地址支持3种匹配符： 
?：匹配文件名中的一个字符 
*：匹配文件名中任意字符 
**：匹配多层路径 

Spring定义一套资源加载的接口，并提供了实现类。ResourceLoader接口仅有一个getResource(String location)的方法，可以根据一个资源地址加载文件资源。不过这个文件资源仅支持带资源类型前缀的表达式，不支持Ant风格的资源路径表达式。ResourcePatternResolver扩展了ResourceLoader接口，定义了一个新的接口方法：getResources(String locationPattern)，该方法支持带资源类型前缀及Ant风格的资源路径的表达式。PathMatchingResourcePatternResolver是Spring提供了标准实现类。 

Spring为BeanFactory提供了多种实现，最常用的XmlBeanFactory。 
BeanFactory最主要的方法就是getBean(String beanName),该方法从容器中返回特定该名称的Bean。BeanFactory的其他接口： 
ListableBeanFactory：该接口定义了访问容器中Bean基本信息的若干方法 
HierarchicalBeanFactory：父子级联IoC容器的接口，子容器可以通过接口方法访问父容器。 
ConfigurableBeanFactory：增强IoC容器的可定制性，它定义了设置类装载其、属性编辑器、容器初始化后置处理器等方法 
AutowireCapableBeanFactory：定义了将容器中的Bean按某种规则进行自动装配的方法 
SingletonBeanRegistry：定义了允许在运行期间向容器注册单实例Bean的方法 
BeanDefinitionRegistry：Spring配置文件中每一个<bean>节点元素在Spring容器里都通过一个BeanDefinition对象表示，他描述了Bean的配置信息。而BeanDefinition Registry接口提供了向容器手工注册BeanDefinition对象的方法。 

ApplicationContext的主要实现类是ClassPathXmlApplicationContext和FileSystemXmlApplicationContext，前者默认从类路径加载配置文件，后者默认从文件系统中装载配置文件。ApplicationContext继承了HierarchicalBeanFactory和ListableBeanFactory接口。 
ApplicationEventPublisher：让容器拥有发布应用上下文事件的功能，包括容器启动事件、关闭事件等。实现了ApplicationListener事件监听接口的Bean可以接受到容器事件，并对事件进行响应处理。在ApplicationContext抽象实现类AbstractApplicationContext中，我们可以发现存在一个ApplicationEventMulticaster，它负责保存所有监听器，以便在容器产生上下文事件时通知这些事件监听者。 
MessageSource：为应用提供il18n国际化消息访问功能。 
ResourcePatternResolver：所有ApplicationContext实现类都实现了类似于PathMatchingResourcePatternResolver功能，可以通过带前缀的Ant风格的资源文件路径装载Spring的配置文件。 
LifeCycle：该接口提供了start()和stop()两个方法，主要用于控制异步处理过程。在具体使用时，ApplicationContext及具体的Bean都必须同时实现该接口，ApplicationContext会将start/stop的信息传递给容器中所有实现了该接口的Bean，已达到管理和控制JMX、任务调度等目的。 

ConfigurableApplicationContext扩展与ApplicationContext，它新增了refresh()和close()，让ApplicationContext具有启动、刷新和关闭应用上下文的能力。在应用上下文关闭的情况下调用refresh()即可启动应用上下文，在已经启动的状态下，调用refresh()则清理缓存并重新装载配置信息，而调用close()则可关闭应用上下文。 

ApplicationContext和BeanFactory的重大区别：BeanFactory在初始化容器时，并未实例化Bean，直到第一次访问某个Bean时才实例目标Bean；而ApplicationContext则在初始化应用上下文时就实例化所有单实例的Bean。因次，ApplicationContext的初始化事件会比BeanFactory稍长，但之后的调用没有第一次惩罚的问题。 

WebApplicationContext是专门为Web应用准备的，它允许从相对于Web根目录的路径中装载配置文件完成初始化工作。从WebApplicationContext中可以获得ServletContext的引用，整个Web应用上下文对象将作为属性放置到ServletContext中，以便Web应用程序可以访问Srping应用上下文。Spring专门为次提供一个工具类WebApplicationContextUtils，通过该类的getWebApplicationContext(ServletContext sc)方法，既可以从ServletContext中获取WebApplicationContext是实例。在WebApplicationContext中还为Bean添加了三个新的作用域：request作用域、session作用域和global session作用域。而在为Web应用环境下，Bean只有singleton和prototype两种作用域。 
WebApplicationContext定义了一个常量ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE，在上下文启动时，WebApplicationContext实例即以此为键放置在ServletContext的属性列表中。 

ConfigurableApplicationContext允许通过配置的方式实例化WebApplicationContext。 
setServletContext(ServletContext servletContext)：为Spring设置Web应用上下文，以便两者整合 
setConfigLocations(String[] configLocations)：设置Spring配置文件地址，一般情况下，配置文件地址是相对于Web根目录的地址。 

WebApplicationContext的初始化方式和BeanFactory、ApplicationContext有所区别。WebApplicationContext秀奥ServletContext实例，也就是说它必须在拥有Web容器的前提下才能完成启动的工作。 
Spring分别提供了用于启动WebApplicationContext的Servlet的Web容器监听器： 
org.springframework.web.context.ContextLoaderServlet、org.springframework.web.context.ContextLoaderListener两者的内部都实现了启动WebApplicationContext实例的逻辑，我们只要根据Web容器的具体情况选择两只之一，并在web.xml中完成配置就可以了。 

由于WebApplicationContext需要使用日志功能，用户可以将Log4J的配置文件放置到类路径的WEB-INF/classes下，这时Log4J引擎即可顺利启动。Spring为启动Log4J引擎提供了两个类似于启动WebApplicationContext的实现类：Log4jConfigServlet和Log4jConfigListener。 

```xml
<context-param> 
    <param-name>contextConfigLocation</param-name> 
    <paramm-value> 
          /WEB-INF/xxx.xml,/WEB-INF/xxxx.xml 
    </param-value> 
</context-param> 

<context-param> 
    <param-name>log4jConfigLocation</param-name> 
    <paramm-value> 
          /WEB-INF/log4j.properties 
    </param-value> 
</context-param> 

<servlet> 
    <servlet-name>log4jConfigServlet</servlet-name> 
    <servlet-class>org.springframework.web.util.Log4jConfigServlet</servlet-class> 
    <load-on-startup>1</load-on-startup> 
</servlet> 

<servlet> 
    <servlet-name>springContextLoaderServlet</servlet-name> 
    <servlet-class>org.springframework.web.context.ContextLoaderServlet</servlet-class> 
    <load-on-startup>2</load-on-startup> 
</servlet> 
```

通过HierarchicalBeanFactory接口，Spring的IoC容器可以建立父子层级关联的容器体系，子容器可以访问父容器中的Bean，但父容器不能访问子容器的Bean。在容器内，Bean的id必须是唯一的，但子容器可以拥有一个和父容器id相同的bean。父子容器层级体系增强了Spring容器架构的扩展性和灵活性。 

**Bean的生命周期：**
1. 当调用者通过`getBean(beanName)`向容器请求某一个Bean时，若容器注册了`org.springframework.beans.factory.InstantiationAwareBeanPostProcessor`接口，在实例化Bean之前，将调用接口的`postProcessBeforeInstantiation()`方法。 
2. 根据配置情况调用Bean构造函数或工厂方法实例化Bean
3. 若容器注册了`InstantiationAwareBeanPostProcessor`接口，在实例化Bean之后，调用该接口的`postProcessAfterInstantiation()`方法，可在这里对已经实例化的对象进行相关操作。 
4. 若Bean配置了属性信息，容器在这一步着手将配置值设置到Bean对应的属性中，不过在设置每个属性之前先将调用`InstantiationAwareBeanPostProcessor`接口的`postProcessPropertyValues()`方法。 
5. 调用Bean的属性设置方法设置属性值 
6. 若Bean实现了`org.springframework.beans.factory.BeanNameAware`接口，将调用`setBeanName()`接口方法，将配置文件中该Bean对应的名称设置到Bean中。 
7. 若Bean实现了`org.springframework.beans.factory.BeanFactoryAware`接口，将调用setBeanFactory()接口方法，将BeanFactory容器实例设置到Bean中。 
8. 若BeanFactory装配了org.springframework.beans.factory.config.BeanPostProcessor后处理器，将调用BeanPostProcessor的Object postProcessBeforeInitialization(Object bean,String beanName)接口方法对Bean进行加工操作。其中参数bean是当前正在处理的bean，而beanName的hi当前Bean的配置名，返回的对象为加工处理后的Bean。BeanPostProcessor在Spring框架中占有重要地位，为容器提供对Bean进行后加工处理的切入点。 
9. 若Bean实现了InitializingBean的接口，将调用接口的afterPropertiesSet()方法 
10. 若在<bean>通过init-method属性定义了初始化方法，将执行这个方法 
11. BeanPostProcessor后处理器定义了两个方法：其一是postProcessBeforeInitializatiopn()在第8步调用；其二是Object postProcessAfterInitialization(Object bean,String beanName)方法，这个方法在此时调用，容器在此获得对Bean进行加工处理的机会。 
12. 若在<bean>中指定Bean的作用范围为scope='prototype'，将Bean返回给调用者，调用者负责调用者后续生命的管理，Spring不再管理这个Bean的生命周期。若作用范围设置为scope='singleton'，则将Bean放入到Spring IoC容器的缓存池中，并将Bean引用返回给调用者，Spring继续对这些Bean进行后续的生命管理。 
13. 对于scope='singleton'的Bean，当容器关闭时，将触发Spring对Bean的后续生命周期的管理工作，首先，若Bean实现了DisposableBean接口，则将调用接口的afterPropertiesSet()方法，可以在次编写释放资源、记录日志等操作。 
14. 对于`scope='singleton'`的Bean，若通过`<bean>`的`destroy-method`属性指定了Bean的销毁方法，Spring将执行Bean这个方法，完成Bean资源的释放等操作。 

Bean的完整生命周期从Spring容器着手实例化Bean开始，知道最终销毁Bean，这当中经过了许多关键点，每个关键点都涉及特定的方法调用，可以将这些方法大致划分为三类： 
* **Bean自身的方法**：若调用Bean构造函数实例化Bean，调用`setter`设置Bean的属性值以及通过<bean>的`init-method`和`destroy-method`所制定的方法； 
* **Bean级生命周期接口方法**：如`BeanNameAware`、`BeanFactoryAware`、`InitializingBean`和`DisposableBean`，这些接口方法由Bean直接实现 
* **容器级生命周期接口方法**：后处理器接口一般不由Bean本身实现，他们独立于Bean，实现类以容器附加装置的形式注册到Spring容器中并通过接口反射为Spring容器预先识别。Spring容器创建Bean时，这些后处理器都会发生作用，所以这些后处理器的影响是全局的。 

`ApplicationContext`和`BeanFactory`的最大区别在于前者会利用Java的反射机制自动识别出配置文件中定义的`BeanPostProcessor`、`IntantiationAwareBeanPostProcessor`和`BeanFactoryProcessor`，并将他们注册到应用上下文中，而后者需要在代码中通过手工调用`addBeanPostProcessor()`方法进行注册。


### 匿名内部类
匿名内部类适合创建那种只需要一次使用的类。匿名内部类的语法有点奇怪，创建匿名内部类时会立即创建一个该类的实例，这个类定义立即消失，匿名内部类不能重复使用。

定义匿名内部类的格式如下：
```
new 父类构造器（参数列表）|实现接口（）  
{  
 //匿名内部类的类体部分  
}  
```

从上面定义可以看出，匿名内部类必须继承一个父类，或实现一个接口，但最多只能继承一个父类，或实现一个接口。  
关于匿名内部类还有如下两条规则：
1. 匿名内部类不能是抽象类，因为系统在创建匿名内部类的时候，会立即创建内部类的对象。因此不允许将匿名内部类定义成抽象类。
2. 匿名内部类不等定义构造器，因为匿名内部类没有类名，所以无法定义构造器，但匿名内部类可以定义实例初始化块，通过实例初始化块来完成构造器需要完成的事情。

`TestAnonymous.java`
```java
interface Product{
  public double getPrice();
  public String getName();
}

public class TestAnonymous{
  public void test(Product p){
    System.out.println("Buy " + p.getName()+", cost " + p.getPrice());
  }

  public static void main(String[] args) {
    TestAnonymous ta = new TestAnonymous();

    ta.test(new Product(){
      public double getPrice(){
        return 567;
      }
      public String getName(){
        return "AGP Card";
      }
    });
  }
}
```

上面程序中的TestAnonymous类定义了一个test方法，该方法需要一个Product对象作为参数，但Product只是一个接口，无法直接创建对象，因此此处考虑创建一个Product接口实现类的对象传入该方法---如果这个Product接口实现类需要重复使用，则应该经该实现类定义一个独立类；如果这个Product接口实现类只需一次使用，则可采用上面程序中的方式，定义一个匿名内部类。

正如上面程序中看到，定义匿名类不需要class关键字，而是在定义匿名内部类时直接生成该匿名内部类的对象。上面粗体字代码部分就是匿名类的类体部分。

由于匿名内部类不能是抽象类，所以匿名内部类必须实现它的抽象父类或者接口里包含的所有抽象方法。

对于上面创建Product实现类对象的代码，可以拆分成如下代码：

```java
class AnonymousProduct implements Product{
  public double getPrice(){
    return 567;
  }
  public String getName(){
    return "AGP Card";
  }
}

ta.test(new AnonymousProduct());
```

当通过实现接口来创建匿名内部类时，匿名内部类也不能显示创建构造器，因此匿名内部类只有一个隐式的无参数构造器，故new接口名后的括号里不能传入参数值。

但如果通过继承父类来创建匿名内部类是，匿名内部类将拥有和父类相似的构造器，此处的相似指的是拥有相同的形参列表。

```java
abstract class Device {
	private String name;

	public Device() {
	}

	public Device(String name) {
		this.name = name;
	}

	public abstract double getPrice();

    public String getName() { return name; }
    public void setName(String name) { this.name = name ; }
	// 此处省略了name属性的setter和getter方法
}


public class AnonymousInner {

	public void test(Device d) {
		System.out.println("Buy " + d.getName() + "，cost " + d.getPrice());
	}

	public static void main(String[] args) {
		AnonymousInner ai = new AnonymousInner();
		// 调用有参数的构造器创建Device匿名实现类的对象
		ai.test(new Device("电子示波器") {
			// ai.test(new Device("Display") {
			public double getPrice() {
				return 67;
			}
		});

		// 调用无参数的构造器创建Device匿名实现类的对象
		Device d = new Device() {
			// 初始化块
			{
				System.out.println("匿名内部类的初始化块...");
			}

			// 实现抽象方法
			public double getPrice() {
				return 56;
			}
      //重写父类中的普通方法
			public String getName() {
				return "键盘";
			}

		};

		ai.test(d);
	}

}
```

上面程序创建了一个抽象父类Device，这个抽象父类里包含两个构造器：一个无参数的，一个有参数的。当创建以Device 为父类的匿名内部类时，即可以传入参数（如上面程序中第一段粗体字部分），也可以不传入参数（如上面程序中第二段粗体字部分）。

当创建匿名内部类时，必须实现接口或抽象父类里的所有抽象方法。如果有需要，也可以重写父类中的普通方法，如上面程序的第二段粗体字代码部分，匿名内部类重写了抽象父类Device类的getName方法，其中getName方法并不是抽象方法。

如果匿名内部类需要访问外部类的局部变量，则必须使用final修饰符来修饰外部类的局部变量，否则系统将报错。

```java
interface A {
  void test();
}
 public class TestA {
  public static void main(Strign[] args) {
     int age = 0;
     A a = new A() {
      public void test() {
         //下面语句将提示错误：匿名内部类内访问局部变量必须使用final修饰
         System.out.println(age);
      }  
    };
 }
   
}
```
上面程序中粗体子代码是匿名内部类访问了外部类的局部变量，由于age变量没有使用final修饰符修饰，所以粗体字代码将起编译异常。
