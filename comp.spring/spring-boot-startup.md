## Spring Boot启动过程

### 概述

**应用代码**
```java
@SpringBootApplication
public class SampleApplication {
  public static void main(String[] args) throws Exception {
    SpringApplication.run(SampleApplication.class, args);
  }
}
```
应用`main`方法用了`SpringApplication`的静态`run`方法。

**SpringApplication类的静态run方法**

在这个静态方法中，创建`SpringApplication`对象，并调用该对象的`run`方法。
```Java
// SpringApplication.run(Application.class, args)
class SpringApplication {
  //...
  public static ConfigurableApplicationContext run(Object source, String... args) {
  	return run(new Object[] { source }, args);
  }

  public static ConfigurableApplicationContext run(Object[] sources, String[] args) {
  	return new SpringApplication(sources).run(args);
  }
  //...
}
```

### 过程概要
`SpringApplication`类的静态`run`方法：

**构造`SpringApplication`对象**  
创建`SpringApplication`对象,构造函数中调用`initialize`方法   
1. 初始化成员变量 `webApplicationType` (非web/servlet/servlet web/reactive web)  
2. 初始化成员变量 `initializers` (`ApplicationContextInitializer`类型对象的集合)  
3. 初始化成员变量 `listeners` (`ApplicationListener<?>`类型对象的集合)  
4. 初始化成员变量 `mainApplicationClass`  

资源文件`META-INF/spring.factories`  
定义`ApplicationContextInitializer`和`ApplicationListener<?>`集合。
  ```
  //以下内容摘自spring-boot-1.5.1.RELEASE.jar中的资源文件META-INF/spring.factories

  # Application Context Initializers
  org.springframework.context.ApplicationContextInitializer=\
  org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
  org.springframework.boot.context.ContextIdApplicationContextInitializer,\
  org.springframework.boot.context.config.DelegatingApplicationContextInitializer,\
  org.springframework.boot.context.web.ServerPortInfoApplicationContextInitializer

  # Application Listeners
  org.springframework.context.ApplicationListener=\
  org.springframework.boot.ClearCachesApplicationListener,\
  org.springframework.boot.builder.ParentContextCloserApplicationListener,\
  org.springframework.boot.context.FileEncodingApplicationListener,\
  org.springframework.boot.context.config.AnsiOutputApplicationListener,\
  org.springframework.boot.context.config.ConfigFileApplicationListener,\
  org.springframework.boot.context.config.DelegatingApplicationListener,\
  org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener,\
  org.springframework.boot.logging.ClasspathLoggingApplicationListener,\
  org.springframework.boot.logging.LoggingApplicationListener
  ```


**调用`SpringApplication`对象的`run`方法**  
1. 配置属性  
   设置headless模式  
   `configureHeadlessProperty()`;
2. 获取监听器  
   `SpringApplicationRunListeners listeners = getRunListeners(args);`
3. 启动监听器
   `listeners.started();`
4. 创建并刷新容器(重点)
  * 获取或者创建环境  
  * 配置环境  
  * 创建`ApplicationContext`  
  * 加载bean到`ApplicationContext`  
  * 刷新`ApplicationContext`  


### 构造SpringApplication对象
构造函数中调用`initialize`方法，初始化`SpringApplication`对象的成员变量`sources`，`webApplicationType`，`initializers`，`listeners`，`mainApplicationClass`。
```java
public SpringApplication(Object... sources) {
  initialize(sources);
}

private void initialize(Object[] sources) {
  // 0.初始化成员变量 sources
  if (sources != null && sources.length > 0) {
    this.sources.addAll(Arrays.asList(sources));
  }

  // 1.初始化成员变量 webApplicationType
	this.webApplicationType = deduceWebApplication();
  // 2.初始化成员变量 initializers
  setInitializers((Collection) getSpringFactoriesInstances(
      ApplicationContextInitializer.class));
  // 3.初始化成员变量 listeners
  setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));

  // 4.初始化成员变量 mainApplicationClass
  this.mainApplicationClass = deduceMainApplicationClass();
}
```
`sources`的赋值比较简单，就是我们传给`SpringApplication.run`方法的参数。剩下的几个，我们依次来看看是怎么做的。

**1. 初始化成员变量 webApplicationType**

设置应用类型。`webApplicationType`：不是web应用，servlet web应用，reactive web应用。
```java
private WebApplicationType webApplicationType;

public enum WebApplicationType {
	NONE,      // 不是web应用，不启动嵌入式web容器。
	SERVLET,   // servlet web应用，启动嵌入式servletb容器。
	REACTIVE;  // reactive web应用，启动嵌入式web容器。
}

private WebApplicationType deduceWebApplication() {
  if (ClassUtils.isPresent(REACTIVE_WEB_ENVIRONMENT_CLASS, null)
      && !ClassUtils.isPresent(MVC_WEB_ENVIRONMENT_CLASS, null)) {
    return WebApplicationType.REACTIVE;
  }
  for (String className : WEB_ENVIRONMENT_CLASSES) {
    if (!ClassUtils.isPresent(className, null)) {
      return WebApplicationType.NONE;
    }
  }
  return WebApplicationType.SERVLET;
}
```

**2. 初始化成员变量 initializers**

`initializers`成员变量，是一个`ApplicationContextInitializer`类型对象的集合。 顾名思义，`ApplicationContextInitializer`是一个可以用来初始化`ApplicationContext`的接口。

```
setInitializers   - 为initializers赋值
  getSpringFactoriesInstances - 获取ApplicationContextInitializer类型对象的列表。
    SpringFactoriesLoader.loadFactoryNames  - 获取所有Spring Factories的名字
    createSpringFactoriesInstances          - 根据名称创建对象
```


```java
//以下代码摘自：org.springframework.boot.SpringApplication
private List<ApplicationContextInitializer<?>> initializers;

private void initialize(Object[] sources) {
	...
	// 为成员变量initializers赋值
	setInitializers((Collection) getSpringFactoriesInstances(	ApplicationContextInitializer.class));
...
}

public void setInitializers(
	    Collection<? extends ApplicationContextInitializer<?>> initializers) {
	this.initializers = new ArrayList<ApplicationContextInitializer<?>>();
	this.initializers.addAll(initializers);
}
```

可以看到，关键是调用`getSpringFactoriesInstances(ApplicationContextInitializer.class)`，来获取`ApplicationContextInitializer`类型对象的列表。
```java
//以下代码摘自：org.springframework.boot.SpringApplication
private <T> Collection<? extends T> getSpringFactoriesInstances(Class<T> type) {
	return getSpringFactoriesInstances(type, new Class<?>[] {});
}

private <T> Collection<? extends T> getSpringFactoriesInstances(Class<T> type,
		Class<?>[] parameterTypes, Object... args) {
	ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
	// Use names and ensure unique to protect against duplicates
	Set<String> names = new LinkedHashSet<String>(
			SpringFactoriesLoader.loadFactoryNames(type, classLoader));
	List<T> instances = createSpringFactoriesInstances(type, parameterTypes,
			classLoader, args, names);
	AnnotationAwareOrderComparator.sort(instances);
	return instances;
}
```
在该方法中，首先通过调用`SpringFactoriesLoader.loadFactoryNames(type,classLoader)`来获取所有Spring Factories的名字，然后调用`createSpringFactoriesInstances`方法根据读取到的名字创建对象。最后会将创建好的对象列表排序并返回。
```java
// 以下代码摘自：org.springframework.core.io.support.SpringFactoriesLoader
public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";

public static List<String> loadFactoryNames(Class<?> factoryClass, ClassLoader classLoader) {
	String factoryClassName = factoryClass.getName();
	try {
		Enumeration<URL> urls = (classLoader != null ? classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
		ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
		List<String> result = new ArrayList<String>();
		while (urls.hasMoreElements()) {
			URL url = urls.nextElement();
			Properties properties = PropertiesLoaderUtils.loadProperties(new UrlResource(url));
			String factoryClassNames = properties.getProperty(factoryClassName);

			result.addAll(Arrays.asList(StringUtils.commaDelimitedListToStringArray(factoryClassNames)));
		}
    return result;
	}
	catch (IOException ex) {
		throw new IllegalArgumentException("Unable to load [" + factoryClass.getName() +
	"] factories from location [" + FACTORIES_RESOURCE_LOCATION + "]", ex);
	}
}
```

可以看到，是从一个名字叫`spring.factories`的资源文件中，读取key为`org.springframework.context.ApplicationContextInitializer`的`value`。而`spring.factories`的部分内容如下：
```
//以下内容摘自spring-boot-1.5.1.RELEASE.jar中的资源文件META-INF/spring.factories
# Application Context Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
org.springframework.boot.context.ContextIdApplicationContextInitializer,\
org.springframework.boot.context.config.DelegatingApplicationContextInitializer,\
org.springframework.boot.context.web.ServerPortInfoApplicationContextInitializer
```
可以看到，最近的得到的，是`ConfigurationWarningsApplicationContextInitializer`，`ContextIdApplicationContextInitializer`，`DelegatingApplicationContextInitializer`，`ServerPortInfoApplicationContextInitializer`这四个类的名字。

接下来会调用`createSpringFactoriesInstances`来创建`ApplicationContextInitializer`实例。
```java
//以下代码摘自：org.springframework.boot.SpringApplication
private <T> List<T> createSpringFactoriesInstances(Class<T> type,
		Class<?>[] parameterTypes, ClassLoader classLoader, Object[] args,
		Set<String> names) {
	List<T> instances = new ArrayList<T>(names.size());
	for (String name : names) {
		try {
			Class<?> instanceClass = ClassUtils.forName(name, classLoader);
			Assert.isAssignable(type, instanceClass);
			Constructor<?> constructor = instanceClass.getConstructor(parameterTypes);
      T instance = (T) constructor.newInstance(args);
      instances.add(instance);
		}
		catch (Throwable ex) {
			throw new IllegalArgumentException(
	"Cannot instantiate " + type + " : " + name, ex);
		}
	}
	return instances;
}
```
所以在我们的例子中，`SpringApplication`对象的成员变量`initalizers`就被初始化为，`ConfigurationWarningsApplicationContextInitializer`，`ContextIdApplicationContextInitializer`，`DelegatingApplicationContextInitializer`，`ServerPortInfoApplicationContextInitializer`这四个类的对象组成的`list`。

**3. 初始化成员变量 listeners**

`listeners`成员变量，是一个`ApplicationListener<?>`类型对象的集合。
```java
//以下代码摘自：org.springframework.boot.SpringApplication
private List<ApplicationListener<?>> listeners;
private void initialize(Object[] sources) {
  ...
  // 为成员变量listeners赋值
	setListeners((Collection)getSpringFactoriesInstances(ApplicationListener.class));
	...
}
public void setListeners(Collection<? extends ApplicationListener<?>> listeners) {
	this.listeners = new ArrayList<ApplicationListener<?>>();
	this.listeners.addAll(listeners);
}
```
`listeners`成员变量，是一个`ApplicationListener<?>`类型对象的集合。可以看到获取该成员变量内容使用的是跟成员变量`initializers`一样的方法，只不过传入的类型从`ApplicationContextInitializer.class`变成了`ApplicationListener.class`。

看一下`spring.factories`中的相关内容：
```
//以下内容摘自spring-boot-1.5.1.RELEASE.jar中的资源文件META-INF/spring.factories
# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.ClearCachesApplicationListener,\
org.springframework.boot.builder.ParentContextCloserApplicationListener,\
org.springframework.boot.context.FileEncodingApplicationListener,\
org.springframework.boot.context.config.AnsiOutputApplicationListener,\
org.springframework.boot.context.config.ConfigFileApplicationListener,\
org.springframework.boot.context.config.DelegatingApplicationListener,\
org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener,\
org.springframework.boot.logging.ClasspathLoggingApplicationListener,\
org.springframework.boot.logging.LoggingApplicationListener
```
也就是说，在我们的例子中，`listener`最终会被初始化为`ParentContextCloserApplicationListener`，`FileEncodingApplicationListener`，`AnsiOutputApplicationListener`，`ConfigFileApplicationListener`，`DelegatingApplicationListener`，`LiquibaseServiceLocatorApplicationListener`，`ClasspathLoggingApplicationListener`，`LoggingApplicationListener`这几个类的对象组成的`list`。

**4. 初始化成员变量 mainApplicationClass**

通过获取当前调用栈，找到入口方法`main`所在的类，并将其复制给`SpringApplication`对象的成员变量`mainApplicationClass`。
```java
//以下代码摘自：org.springframework.boot.SpringApplication
private Class<?> mainApplicationClass;
private void initialize(Object[] sources) {
  ...
  // 为成员变量mainApplicationClass赋值
  this.mainApplicationClass = deduceMainApplicationClass();
  ...
}

private Class<?> deduceMainApplicationClass() {
  try {
    StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
    for (StackTraceElement stackTraceElement : stackTrace) {
      if ("main".equals(stackTraceElement.getMethodName())) {
        return Class.forName(stackTraceElement.getClassName());
      }
    }
  }
  catch (ClassNotFoundException ex) {
    // Swallow and continue
  }
  return null;
}
```
在`deduceMainApplicationClass`方法中，通过获取当前调用栈，找到入口方法`main`所在的类，并将其复制给`SpringApplication`对象的成员变量`mainApplicationClass`。在我们的例子中`mainApplicationClass`即是我们自己编写的`Application`类。

### SpringApplication对象的run方法



---

`SpringApplication`用来从java `main`方法启动一个spring应用，默认的启动步骤如下：
1. 创建一个合适的`ApplicationContext`实例,这个实例取决于`classpath`。
2. 注册一个`CommandLinePropertySource`，以spring属性的形式来暴露命令行参数。
3. 刷新`ApplicationContext`，加载所有的单例bean。
4. 触发所有的命令行`CommanLineRunner`来执行bean。

springApplication可以读取不同种类的源文件：
* 类- java类由AnnotatedBeanDefinitionReader加载。
* Resource - xml资源文件由XmlBeanDefinitionReader读取, 或者groovy脚本由GroovyBeanDefinitionReader读取
* Package - java包文件由ClassPathBeanDefinitionScanner扫描读取。
* CharSequence - 字符序列可以是类名、资源文件、包名，根据不同方式加载。如果一个字符序列不可以解析程序到类，也不可以解析到资源文件，那么就认为它是一个包。


**SpringApplication 运行方法run**  
````
1. 配置属性  
2. 监听器
2.1 获取监听器
2.2 启动监听
2.3 结束监听
3. 容器（创建/准备/刷新）  
3.1 应用参数  
3.2 创建并配置环境  
3.3 横幅Banner
3.4 创建并刷新容器
3.4.1 创建容器 ApplicationContext  
3.4.2 准备好容器 (环境/监听/应用参数/横幅)
3.4.3 刷新容器   
````

**SpringApplication方法run 总体流程**
```java
public ConfigurableApplicationContext run(String... args) {
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    ConfigurableApplicationContext context = null;
  	FailureAnalyzers analyzers = null;

    //1.配置属性。设置Headless模式
    configureHeadlessProperty();
    //2. 监听器 (SpringApplicationRunListeners)
    //2.1获取监听器
    SpringApplicationRunListeners listeners = getRunListeners(args);
    //2.2启动监听
    listeners.starting();
    try {
      //==3.创建/准备/刷新容器==
      //3.1 应用参数
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(
        args);
      //3.2 创建并配置Environment
      ConfigurableEnvironment environment = prepareEnvironment(listeners,
      		applicationArguments);
      configureIgnoreBeanInfo(environment);
      bindToSpringApplication(environment);
      //3.3 横幅Banner
      Banner printedBanner = printBanner(environment);

      //3.4 创建并刷新容器
      //3.4.1 创建新容器
      context = createApplicationContext();
      analyzers = new FailureAnalyzers(context);
      //3.4.2 准备好容器 (环境/监听/应用参数/横幅)
      prepareContext(context, environment, listeners, applicationArguments,
  				printedBanner);
      //3.4.3 刷新容器
      refreshContext(context);
      afterRefresh(context, applicationArguments);
      //2.3 结束监听
      listeners.finished(context, null);
      stopWatch.stop();
      if (this.logStartupInfo) {
        new StartupInfoLogger(this.mainApplicationClass)
            .logStarted(getApplicationLog(), stopWatch);
      }
      return context;
    }
    catch (Throwable ex) {
      handleRunFailure(context, listeners, ex);
      throw new IllegalStateException(ex);
    }
}
```

1. 配置属性
```java
private void configureHeadlessProperty() {
  System.setProperty(SYSTEM_PROPERTY_JAVA_AWT_HEADLESS, System.getProperty(
    SYSTEM_PROPERTY_JAVA_AWT_HEADLESS, Boolean.toString(this.headless)));
}
```

2.1 获取监听器
```java
private SpringApplicationRunListeners getRunListeners(String[] args) {
  Class<?>[] types = new Class<?>[] { SpringApplication.class, String[].class };
  return new SpringApplicationRunListeners(logger, getSpringFactoriesInstances(
    SpringApplicationRunListener.class, types, this, args));
}
```

2.2 启动监听器
```java
public void started() {
   for (SpringApplicationRunListener listener : this.listeners) {
   listener.started();
   }
}
```

3 创建并刷新容器(重点)
```java
private ConfigurableApplicationContext createAndRefreshContext(
  SpringApplicationRunListeners listeners,
      ApplicationArguments applicationArguments) {
  ConfigurableApplicationContext context;
  // Create and configure the environment
  ConfigurableEnvironment environment = getOrCreateEnvironment();
  configureEnvironment(environment, applicationArguments.getSourceArgs());
  listeners.environmentPrepared(environment);
  if (isWebEnvironment(environment) && !this.webEnvironment) {
    environment = convertToStandardEnvironment(environment);
  }

  if (this.bannerMode != Banner.Mode.OFF) {
    printBanner(environment);
  }

  // Create, load, refresh and run the ApplicationContext
  context = createApplicationContext();
  context.setEnvironment(environment);
  postProcessApplicationContext(context);
  applyInitializers(context);
  listeners.contextPrepared(context);
  if (this.logStartupInfo) {
    logStartupInfo(context.getParent() == null);
    logStartupProfileInfo(context);
  }

  // Add boot specific singleton beans
  context.getBeanFactory().registerSingleton("springApplicationArguments",
    applicationArguments);

  // Load the sources
  Set<Object> sources = getSources();
  Assert.notEmpty(sources, "Sources must not be empty");
  load(context, sources.toArray(new Object[sources.size()]));
  listeners.contextLoaded(context);

  // Refresh the context
  refresh(context);
  if (this.registerShutdownHook) {
    try {
      context.registerShutdownHook();
    }
    catch (AccessControlException ex) {
      // Not allowed in some environments.
    }
  }
  return context;
}
```


**推送ApplicationStartedEvent**

**准备Environment**

**创建及准备ApplicationContext**

### 内置类说明
**LoggingApplicationListener**


**ConfigFileApplicationListener**


**ApplicationContextAwareProcessor**  
ApplicationContextAwareProcessor实现了BeanPostProcessor接口，根据javadoc这个类用来调用以下接口的回调方法：
1. EnvironmentAware
2. EmbeddedValueResolverAware
3. ResourceLoaderAware
4. ApplicationEventPublisherAware
5. MessageSourceAware
6. ApplicationContextAware

**AnnotationConfigApplicationContext**  
这个类用来将`@Configuration`和`@Component`作为输入来注册`BeanDefinition`。

**AnnotatedBeanDefinitionReader**
AnnotatedBeanDefinitionReader在其构造函数内部间接(AnnotationConfigUtils#L145)的给BeanFactory注册了几个与BeanDefinition相关注解的处理器。
1. ConfigurationClassPostProcessor
2. AutowiredAnnotationBeanPostProcessor
3. RequiredAnnotationBeanPostProcessor
4. CommonAnnotationBeanPostProcessor
5. EventListenerMethodProcessor
6. PersistenceAnnotationBeanPostProcessor
