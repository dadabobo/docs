
## Spring in Action

## 第三章 高级装配
展示Spring的一些新特性，使用最少的XML装配应用对象。

### 1. 环境与profile
#### 配置profile bean
使用`@profile`注解指定某个bean属于哪个profile。可以在类级别上和方法级别上使用`@Profile`注解。

```java
@Configuration
public class DataSourceConfig {

  @Bean(destroyMethod = "shutdown")
  @Profile("dev")
  public DataSource embeddedDataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .addScript("classpath:schema.sql")
        .addScript("classpath:test-data.sql")
        .build();
  }

  @Bean
  @Profile("prod")
  public DataSource jndiDataSource() {
    JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
    jndiObjectFactoryBean.setJndiName("jdbc/myDS");
    jndiObjectFactoryBean.setResourceRef(true);
    jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
    return (DataSource) jndiObjectFactoryBean.getObject();
  }

}
```

你还可以在根`<beans>`元素中嵌套定义`<beans>`元素，而不是为每个环境都创建一个profile XML文件。这能够将所有的profile
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
  xmlns:jee="http://www.springframework.org/schema/jee" xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="
    http://www.springframework.org/schema/jee
    http://www.springframework.org/schema/jee/spring-jee.xsd
    http://www.springframework.org/schema/jdbc
    http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

  <beans profile="dev">
    <jdbc:embedded-database id="dataSource" type="H2">
      <jdbc:script location="classpath:schema.sql" />
      <jdbc:script location="classpath:test-data.sql" />
    </jdbc:embedded-database>
  </beans>

  <beans profile="prod">
    <jee:jndi-lookup id="dataSource"
      lazy-init="true"
      jndi-name="jdbc/myDatabase"
      resource-ref="true"
      proxy-interface="javax.sql.DataSource" />
  </beans>
</beans>
```

#### 激活profile
Spring在确定哪个profile处于激活状态时，需要依赖两个独立的属性：`spring.profiles.active`和`spring.profiles.default`。如 果设置了`spring.profiles.active`属性的话，那么它的值就会用来确定哪个profile是激活的。但如果没有设置`spring.profiles.active`属性的话，那Spring将会查找`spring.profiles.default`的值。如果`spring.profiles.active`和`spring.profiles.default`均没有设置的话，那就没有激活的profile，因此只会创建那些没有定义在 profile中的bean。  
有多种方式来设置这两个属性：
* 作为DispatcherServlet的初始化参数；
* 作为Web应用的上下文参数；
* 作为JNDI条目；
* 作为环境变量；
* 作为JVM的系统属性；
* 在集成测试类上，使用@ActiveProfiles注解设置。

当设置`spring.profiles.active`以后，至于`spring.profiles.default`置成什么值就已经无所谓了；系统会优先使 用`spring.profiles.active`中所设置的profile。

#### 使用profile进行测试
Spring提供了`@ActiveProfiles`注解，我们可以使用它来指定运行测试时要激活哪个profile。在集成测试时，通常想要激活的是开发环境的 profile。

### 2. 条件化的bean
`@Conditional`注解，它可以用到带有`@Bean`注解的方法上。如果给定的条件计算结果为`true`，就会创建这个bean，否则的话，这个bean会被忽略。

```java
@Bean
@Conditional(MagicExistsCondition.class)
public MagicBean magicBean() {
    return new MagicBean();
}
```

`Condition`接口
```java
public interface Condition {
    boolean matches(ConditionContext ctxt, AnnotatedTypeMetadata metadata);
}
```

`CondiitionContext`接口
```java
public interface ConditionContext {
  BeanDefinitionRegistry getRegistry();
  ConfigurableListableBeanFactory getBeanFactory();
  Environment getEnvironment();
  ResourceLoader getResourceLoader();
  ClassLoader getClassLoader();
}
```

通过`ConditionContext`，我们可以做到如下几点：
* 借助`getRegistry()` 返回的`BeanDefinitionRegistry`检查bean定义；
* 借助`getBeanFactory()` 返回的`ConfigurableListableBeanFactory`检查bean是否存在，甚至探查bean的属性；
* 借助`getEnvironment()` 返回的`Environment`检查环境变量是否存在以及它的值是什么；
* 读取并探查`getResourceLoader()` 返回的`ResourceLoader`所加载的资源；
* 借助`getClassLoader()`返回的`ClassLoader`加载并检查类是否存在。

### 3. 处理自动装配的歧义性
仅有一个bean匹配所需的结果时，自动装配才是有效的。如果不仅有一个bean能够匹配结果的话，这种歧义性会阻碍Spring自动装配属性、构造器参数或方法参数。

#### 标示首选的bean
在声明bean的时候，通过将其中一个可选的bean设置为首选（primary）bean能够避免自动装配时的歧义性。
`@Primary`能够与`@Component`组合用在组件扫描的bean上，也可以与`@Bean`组合用在Java配置的bean声明中。
```java
@Component
@Primary
public class IceCream implements Dessert { ... }
```

#### 限定自动装配的bean
`@Qualifier`注解是使用限定符的主要方式。它可以与`@Autowired`和`@Inject`协同使用，在注入的时候指定想要注入进去的是哪个bean。`iceCream`是bean的ID，作为限定符。

```java
@Autowired
@Qualifier("iceCream")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

`@Component` 与 `@Qualifier`自定义限定符，`@Autowired` + `@Qualifier`使用自定义限定符。  
```java
@Component
@Qualifier("cold")
public class IceCream implements Dessert { ... }

@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

Java不允许在同一个条目上重复出现相同类型的多个注解。

### 4. bean的作用域

在默认情况下，Spring应用上下文中所有bean都是作为以单例（singleton）的形式创建的。  
Spring定义了多种作用域，可以基于这些作用域创建bean，包括：
* 单例（Singleton）：在整个应用中，只创建bean的一个实例。
* 原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例。
* 会话（Session）：在Web应用中，为每个会话创建一个bean实例。
* 请求（Rquest）：在Web应用中，为每个请求创建一个bean实例。

选择作用域，要使用`@Scope`注解，它可以与`@Component`或@Bean一起使用。

### 5. 运行时值注入

Spring提供了两种在运行时求值的方式：
* 属性占位符（Property placeholder）。
* Spring表达式语言（SpEL）。

处理外部值的最简单方式就是声明属性源(`@PropertySource`)并通过Spring的`Environment`来检索属性。
```java
@Configuration
@PropertySource("classpath:/com/soundsystem/app.properties")
public class EnvironmentConfig {

  @Autowired
  Environment env;

  @Bean
  public BlankDisc blankDisc() {
    return new BlankDisc(
        env.getProperty("disc.title"),
        env.getProperty("disc.artist"));
  }

}
```

`Environment`支持属性相关的功能，还提供了一些方法来检查哪些profile处于激活状态。
* String getProperty(String key)
* String getProperty(String key, String defaultValue)
* T getProperty(String key, Class<T> type)
* T getProperty(String key, Class<T> type, T defaultValue)
* `String[] getActiveProfiles()`：返回激活profile名称的数组；
* `String[] getDefaultProfiles()`：返回默认profile名称的数组；
* `boolean acceptsProfiles(String... profiles)`：如果environment支持给定profile的话，就返回`true`。


#### 解析属性占位符  
属性占位符需要放到` “${ ... }” `之中。
```xml
<bean id="sgtPeppers"
      class="soundsystem.BlankDisc"
      c:_title="${disc.title}"
      c:_artist="${disc.artist}" />
```

```java
ublic BlankDisc(
      @Value("${disc.title}") String title,
      @Value("${disc.artist}") String artist) {
  this.title = title;
  this.artist = artist;
}
```



#### 使用Spring表达式语言进行装配
Spring表达式语言（Spring Expression Language，SpEL），能够以一种强大和简洁的方式将值装配到bean属性和构造器参数中，在这个过程中所使用的表达式会在运行时计算得到值。使用SpEL，你可以实现超乎想象的装配效果，这是使用其他的装配技术难以做到的（甚至是不可能的）。  

SpEL的特性：
* 使用bean的ID来引用bean；
* 调用方法和访问对象的属性；
* 对值进行算术、关系和逻辑运算；
* 正则表达式匹配；
* 集合操作。

SpEL使用场景：
* 依赖注入；
* Spring Security支持使用SpEL表达式定义安全限制规则；
* 在Spring MVC应用中使用Thymeleaf模板作为视图的话，那么这些模板可以使用SpEL表达式引用模型数据；

SpEL表达式要放到` “#{ ... }” `之中。

在bean装配的时候如何使用SpEL表达式。  
如果通过组件扫描创建bean的话，在注入属性和构造器参数时，我们可以使用`@Value`注解，这与之前看到的属性占位符非常类似。不过，在这里我们所使用的不是占位符表达式，而是SpEL表达式。
```java
public BlankDisc(
      @Value("#{systemProperties['disc.title']}") String title,
      @Value("#{systemProperties['disc.artist']}") String artist) {
  this.title = title;
  this.artist = artist;
}
```

在`XML`配置中，你可以将`SpEL`表达式传入`<property>`或`<constructor-arg>`的`value`属性中，或者将其作为`p-`命名空间或`c-`命名空间条目的值。
```xml
<bean id="sgtPeppers"
      class="soundsystem.BlankDisc"
      c:_title="#{systemProperties['disc.title']}"
      c:_artist="#{systemProperties['disc.artist']}" />
```

表示字面值: `#{3.14159}`、`#{9.87E4}`、`#{'Hello'}`、`#{false}`。

引用bean、属性和方法：
```spel
#{sgtPeppers}
#{sgtPeppers.artist}
#{artistSelector.selectArtist()}
#{artistSelector.selectArtist().toUpperCase()}
#{artistSelector.selectArtist()?.toUpperCase()}
```

如果要在SpEL中访问类作用域的方法和常量的话，要依赖T()这个关键的运算符。
`T(java.lang.Math)` `T()`运算符的结果会是一个Class对象，代表了`java.lang.Math`。
如果需要可以将其装配到一个Class类型的bean属性中。但是`T()`运算符的真正价值在于它能够访问目标类型的静态方法和常量。
```java
T(java.lang.Math).PI
```

SpEL提供了多个运算符，这些运算符可以用在SpEL表达式的值上。包括：算术运算、比较运算、逻辑运算、条件运算、正则表达式。
