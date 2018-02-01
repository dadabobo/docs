
## 第二章 装配Bean

使用Spring基于XML的配置，实现应用对象借助依赖注入保持松耦合。
学习定义应用对象并装配其依赖类。

在Spring中，对象无需自己查找或创建与其所关联的其他对象。相反，容器负责把需要相互协作的对象引用赋予各个对象。
创建应用对象之间协作关系的行为通常称为装配（wiring），这也是依赖注入（DI）的本质。

### 1. Spring配置的可选方案

在Spring中装配bean有多种方式,三种主要的装配机制：
* 在XML中进行显式配置。
* 在Java中进行显式配置。
* 隐式的bean发现机制和自动装配。
Spring的配置风格是可以互相搭配。

建议是尽可能地使用自动配置的机制。显式配置越少越好。

### 2. 自动化装配bean
Spring从两个角度来实现自动化装配：
* 组件扫描（component scanning）：Spring会自动发现应用上下文中所创建的bean。
* 自动装配（autowiring）：Spring自动满足bean之间的依赖。

#### 创建可被发现的bean

`CompactDisc` 接口在Java中定义了CD的概念  
`CompactDisc.java`
```java
public interface CompactDisc {
  void play();
}
```

带有`@Component`注解的`CompactDisc`实现类`SgtPeppers`  
`SgtPeppers.java`
```java
@Component
public class SgtPeppers implements CompactDisc {

  private String title = "Sgt. Pepper's Lonely Hearts Club Band";  
  private String artist = "The Beatles";

  public void play() {
    System.out.println("Playing " + title + " by " + artist);
  }

}
```
`@Component`注解表明该类会作为组件类，并告知Spring要为这个类创建bean。

组件扫描默认是不启用的。我们还需要显式配置一下Spring，从而命令它去寻找带有`@Component`注解的类，并为其创建bean。

**`@ComponentScan`注解启用了组件扫描**  
`CDPlayerConfig.java`
```java
@Configuration
@ComponentScan
public class CDPlayerConfig {
}
```
如果没有其他配置的话，`@ComponentScan`默认会扫描与配置类相同的包。

**通过XML启用组件扫描**
```xml
<beans>
  <context:component-scan base-package="soundsystem" />
</beans>
```
`<context:component-scan>`元素会有与`@ComponentScan`注解相对应的属性和子元素。

测试组件扫描能够发现`CompactDisc`   
`CDPlayerTest.java`
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=CDPlayerConfig.class)
public class CDPlayerTest {

  @Rule
  public final StandardOutputStreamLog log = new StandardOutputStreamLog();

  @Autowired
  private MediaPlayer player;

  @Autowired
  private CompactDisc cd;

  @Test
  public void cdShouldNotBeNull() {
    assertNotNull(cd);
  }

  @Test
  public void play() {
    player.play();
    assertEquals(
        "Playing Sgt. Pepper's Lonely Hearts Club Band by The Beatles\n",
        log.getLog());
  }

}
```
使用了Spring的`SpringJUnit4ClassRunner`，以便在测试开始的时候自动创建Spring的应用上下文。

属性带有`@Autowired`注解，以便于将`CompactDiscbean`注入到测试代码之中。

#### 为组件扫描的bean命名

Spring应用上下文中所有的bean都会给定一个ID。Spring会根据类名为其指定一个ID。就是将类名的第一个字母变为小写。

可以将ID作为值传递给`@Component`注解。(如`lonelyHeartsClub`)

```java
@Component("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
    ...
}
```

还有另外一种为bean命名的方式，不使用`@Component`注解，而是使用Java依赖注入规范中所提供的`@Named`注解来为bean设置ID：
```java
@Named("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
    ...
}
```

#### 设置组件扫描的基础包

`@ComponentScan` 按照默认规则，它会以配置类所在的包作为基础包来扫描组件。

通过basePackages属性进行配置多个基础包。

```java
@Configuration
@ComponentScan(basePackages={"soundsystem", "video"})
public class CDPlayerConfig {}
```

除了将包设置为简单的`String`类型之外，`@ComponentScan`还提供了另外一种方法，那就是将其指定为包中所包含的类或接口.
```java
@Configuration
@ComponentScan(basePackagesClasses={CDPlayer.class, DVDPlayer.class})
public class CDPlayerConfig {}
```

#### 通过为bean添加注解实现自动装配

自动装配就是让Spring自动满足bean依赖的一种方法。在满足依赖的过程中，会在Spring应用上下文中寻找匹配某个bean需求的其他bean。为了声明要进行自动装配，我们可以借助Spring的`@Autowired`注解。  
`MediaPlayer.java`
```java
public interface MediaPlayer {
  void play();
}
```

`CDPlayer.java`
```java
@Component
public class CDPlayer implements MediaPlayer {
  private CompactDisc cd;

  @Autowired
  public CDPlayer(CompactDisc cd) {
    this.cd = cd;
  }

  public void play() {
    cd.play();
  }

}
```
`CDPlayer`类的构造器上添加了`@Autowired`注解，这表明当Spring创建`CDPlayer` bean的时候，会通过这个构造器来进行实例化并且会传入一个可设置给`CompactDisc`类型的bean。  
`@Autowired`注解不仅能够用在构造器上，还能用在属性的`Setter`方法上。

在Spring初始化bean之后，它会尽可能得去满足bean的依赖。
不管是构造器、Setter方法还是其他的方法，Spring都会尝试满足方法参数上所声明的依赖。
假如有且只有一个bean匹配依赖需求的话，那么这个bean将会被装配进来。
如果没有匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。

`@Autowired`是Spring特有的注解。如果你不愿意在代码中到处使用Spring的特定注解来完成自动装配任务的话，那么你可以考虑将其替换为 `@Inject`。
`@Inject`注解来源于Java依赖注入规范，该规范同时还为我们定义了`@Named`注解。在自动装配中，Spring同时支持`@Inject`和`@Autowired`。尽管`@Inject`和`@Autowired`之间有着一些细微的差别，但是在大多数场景下，它们都是可以互相替换的。

#### 验证自动装配

`CDPlayerConfig.java`
```java
@Configuration
@ComponentScan
public class CDPlayerConfig {
}
```

`AutoconfigApp.java`
```java
public class AutoconfigApp {

  public static void main(String[] args) {
    AnnotationConfigApplicationContext ctx =
        new AnnotationConfigApplicationContext(CDPlayerConfig.class);

    MediaPlayer play = ctx.getBean(MediaPlayer.class);
    play.play();
  }

}
```

### 3. 通过Java代码装配bean

尽管通过组件扫描和自动装配实现Spring的自动化配置是更为推荐的方式，但有时候自动化配置的方案行不通，因此需要明确配置Spring。比如说，你想要将第三方库中的组件装配到你的应用中。

在进行显式配置的时候，有两种可选方案：Java和XML。
在进行显式配置时，JavaConfig是更好的方案，因为它更为强大、类型安全并且对重构友好。

同时，JavaConfig与其他的Java代码又有所区别，在概念上，它与应用程序中的业务逻辑和领域代码是不同的。尽管它与其他的组件一样都使用相同的语言进行表述，但JavaConfig是配置代码。
这意味着它不应该包含任何业务逻辑，JavaConfig也不应该侵入到业务逻辑代码之中。  

**创建配置类**  
`CDPlayerConfig.java`
```java
@Configuration
public class CDPlayerConfig {

    @Bean
    public CompactDisc compactDisc() {
        return new SgtPeppers();
    }

    @Bean
    public CDPlayer cdPlayer(CompactDisc compactDisc) {
        return new CDPlayer(compactDisc);
    }

}
```

* 移除了`@ComponentScan`注解，此时的CDPlayerConfig类就没有任何作用了。
* `@Configuration`注解表明这是一个装配类。该类应该包含在Spring应用上下文中如何创建bean的细节。
* `@Bean`注解会告诉Spring这个方法将会返回一个对象，该对象要注册为Spring应用上下文中的bean。
* 默认情况下，bean的ID与带有`@Bean`注解的方法名是一样的。
* 在`JavaConfig`中装配bean的最简单方式就是引用创建bean的方法。
带有`@Bean`注解的方法可以采用任何必要的Java功能来产生bean实例。


### 4. 通过XML装配bean

#### 创建XML配置规范

在XML配置中，这意味着要创建一个XML文件，并且要以<beans>元素为根。
在使用XML时，需要在配置文件的顶部声明多个XML模式（XSD）文件，这些文件定义了配置Spring的XML元素。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:c="http://www.springframework.org/schema/c"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <!-- configuration details go here -->

</beans>
```

用来装配bean的最基本的XML元素包含在spring-beans模式之中，在上面这个XML文件中，它被定义为根命名空间。<beans>是该模式中的一个元素，它是所有Spring配置文件的根元素。  

#### 声明一个简单的bean
```xml
`<bean class="soundsystem.SgtPeppers" />` 
```
声明了一个很简单的bean，创建这个bean的类通过class属性来指定的，并且要使用全限定的类名。
因为没有明确给定ID，这个bean将会根据全限定类名来进行命名。在本例中，bean的ID将会是 `soundsystem.SgtPeppers#0` 。其中，`#0` 是一个计数的形式，用来区分相同类型的其他bean。如果你声明了另外一个SgtPeppers，并且没有明确进行标识，那么它自动得到`soundsystem.SgtPeppers#1`。

#### 借助构造器注入初始化bean

在XML中声明DI时，会有多种可选的配置方案和风格。具体到构造器注入，有两种基本的配置方案可供选择：
* `<constructor-arg>`元素
* 使用Spring 3.0所引入的`c-命名空间`

`<constructor-arg>`元素
```xml
  <bean id="compactDisc" class="soundsystem.SgtPeppers" />
  <bean id="cdPlayer" class="soundsystem.CDPlayer">
      <constructor-arg="compactDisc" />
  </bean>
```

`c-`命名空间
```xml
  <bean id="compactDisc" class="soundsystem.SgtPeppers" />
  <bean id="cdPlayer" class="soundsystem.CDPlayer"
        c:cd-ref="compactDisc" />
```
通过Spring的c-命名空间将bean引用注入到构造器参数中: `c:cd-ref="comactDisc"`
* `c` 名字空间前缀
* `cd` 要装配的构造器参数名称
* `-ref` 一个命名的约定，表示注入bean引用。
* `"compactDisc"` 要注入的bean的ID

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer"
      c:_0-ref="compactDisc" />
```
`_0` 参数的索引。使用索引来识别构造器参数感觉比使用名字更好一些。

```xml
<bean id="compactDisc"
      class="soundsystem.BlankDisc">
  <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
  <constructor-arg value="The Beatles" />
</bean>
```
使用`<constructor-arg>`元素进行构造器参数的注入。但是这一次我们没有使用`“ref”`属性来引用其他的bean，而是使用了`value`属性，通过该属性表明给定的值要以字面量的形式注入到构造器之中。

```xml
<bean id="compactDisc"
      class="soundsystem.properties.BlankDisc"
      p:title="Sgt. Pepper's Lonely Hearts Club Band"
      p:artist="The Beatles">
```

在装配集合方面，`<constructor-arg>`比`c-`命名空间的属性更有优势。目前，使用`c-`命名空间的属性无法实现装配集合的功能。

```
Chapter_02\stereo-xmlconfig\src\test\resources\soundsystem\
    CNamespaceReferenceTest-context.xml             c-命名空间 引用
    CNamespaceValueTest-context.xml                 c-命名空间 值
    ConstructorArgReferenceTest-context.xml         <constructor-arg>元素 引用
    ConstructorArgValueTest-context.xml             <constructor-arg>元素 值
    ConstructorArgCollectionTest-context.xml        <constructor-arg>元素 集合
```

```xml
<!-- 简单声明bean -->
<bean id="compactDisc" class="soundsystem.SgtPeppers" />

<!-- 构造器注入 之 c-命名空间 -->
<!-- [cd]构造参数名; [_0]参数在列表中的位置; [_]一个参数，省略0 -->
<!-- 注入bean引用 -->
<bean id="cdPlayer" class="soundsystem.CDPlayer" c:cd-ref="compactDisc" />
<bean id="cdPlayer" class="soundsystem.CDPlayer" c:_-ref="compactDisc" />      
<bean id="cdPlayer" class="soundsystem.CDPlayer" c:_0-ref="compactDisc" />      
<!-- 注入字面量 -->
<bean id="compactDisc" class="soundsystem.BlankDisc"
    c:_title="Sgt. Pepper's Lonely Hearts Club Band"
    c:_artist="The Beatles" />
<bean id="compactDisc" class="soundsystem.BlankDisc"
    c:_0="Sgt. Pepper's Lonely Hearts Club Band"
    c:_1="The Beatles" />

<!-- 构造器注入 之 <constructor-arg>元素 -->
<constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
<constructor-arg value="The Beatles" />
<constructor-arg>
  <list>
    <value>Sgt. Pepper's Lonely Hearts Club Band</value>
    <value>With a Little Help from My Friends</value>
    <value>Lucy in the Sky with Diamonds</value>
  </list>
</constructor-arg>
</bean>

<bean id="cdPlayer" class="soundsystem.CDPlayer">
<constructor-arg ref="compactDisc" />
</bean>

```

#### 设置属性
该选择构造器注入还是属性注入呢？作为一个通用的规则，我倾向于对强依赖使用构造器注入，而对可选性的依赖使用属性注入。
* `<property>`元素
* `p-`命名空间

```
Chapter_02\stereo-xmlconfig\src\test\resources\soundsystem\
    PamespaceRefTest-context.xml                    <property>元素 引用 
    PNamespaceValueTest-context.xml                 <property>元素 值
    PNamespaceWithUtilNamespaceTest-context.xml     p-命名空间 
    PropertyRefTest-context.xml                     p-命名空间 引用
    PropertyValueTest-context.xml                   p-命名空间 值
```

```xml
  <!-- p-命名空间 -->
  <bean id="cdPlayer" class="soundsystem.properties.CDPlayer"
        p:compactDisc-ref="compactDisc" />
  
  <bean id="compactDisc" class="soundsystem.properties.BlankDisc"
        p:title="Sgt. Pepper's Lonely Hearts Club Band"
        p:artist="The Beatles">
    <property name="tracks">
      <list>
        <value>Sgt. Pepper's Lonely Hearts Club Band</value>
        <value>With a Little Help from My Friends</value>
      </list>
    </property>
  </bean>

  <!-- <property>元素 -->
  <bean id="cdPlayer" class="soundsystem.properties.CDPlayer">
    <property name="compactDisc" ref="compactDisc" />
  </bean>

  <bean id="compactDisc" class="soundsystem.properties.BlankDisc">
    <property name="title" value="Sgt. Pepper's Lonely Hearts Club Band" />
    <property name="artist" value="The Beatles" />
    <property name="tracks">
      <list>
        <value>Sgt. Pepper's Lonely Hearts Club Band</value>
        <value>With a Little Help from My Friends</value>
      </list>
    </property>
  </bean>
```

`util-`命名空间。`util-`命名空间包括

```xml
  <bean id="compactDisc" class="soundsystem.properties.BlankDisc"
        p:title="Sgt. Pepper's Lonely Hearts Club Band"
        p:artist="The Beatles"
        p:tracks-ref="trackList" />

  <util:list id="trackList">  
    <value>Sgt. Pepper's Lonely Hearts Club Band</value>
    <value>With a Little Help from My Friends</value>
    <value>Lucy in the Sky with Diamonds</value>
  </util:list>
```


### 5. 导入和混合配置

#### 在JavaConfig中引用XML配置
使用`@ImportResource`注解
```java
@Configuration
@Import(CDPlayerConfig.class)
@ImportResource("classpath:cd-config.xml")
public class SoundSystemConfig {
}
```

使用`@Import`将两个配置类组合在一起：
```java
@Configuration
@Import(CDPlayerConfig.class, CDConfig.class)
public class SoundSystemConfig {
}
```


#### 在XML配置中引用JavaConfig

使用`<bean>`元素将Java配置导入到XML配置中：
```xml
  <bean class="soundsystem.CDConfig" />

  <bean id="cdPlayer" class="soundsystem.CDPlayer"
        c:cd-ref="compactDisc" />
```

组合两种配置：XML和Java。使用`import`元素。
```xml
  <bean class="soundsystem.CDConfig" />
  <import resource="cdplayer-config.xml" />
```


