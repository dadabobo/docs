
## Spring in Action

## 第四章 面向切面的Spring

如何使用Spring的AOP特性将系统级的服务（如安全和审计）从它们所服务的对象中解耦出来。

### 1. 什么是面向切面编程

#### AOP术语
* 通知（advice）：切面的工作被称为通知。Spring切面可以应用5种类型的通知。
* 连接点（join point）：我们的应用可能也有数以千计的时机应用通知。这些时机被称为连接点。连接点是在应用执行过程中能够插入切面的一个点。这个点可以是调用方法时、抛出异常时、甚至修改一个字段时。切面代码可以利用这些点插入到应用的正常流程之中，并添加新的行为。
* 切点（pointcut）：切点的定义会匹配通知所要织入的一个或多个连接点
* 切面（Aspect）：切面是通知和切点的结合。通知和切点共同定义了切面的全部内容——它是什么，在何时和何处完成其功能。
* 引入（Introduction）：引入允许我们向现有的类添加新方法或属性。
* 织入（Weaving）：织入是把切面应用到目标对象并创建新的代理对象的过程。切面在指定的连接点被织入到目标对象中。

#### Spring对AOP的支持
Spring提供了4种类型的AOP支持：
* 基于代理的经典Spring AOP；
* 纯POJO切面；
* `@AspectJ`注解驱动的切面；
* 注入式AspectJ切面（适用于Spring各版本）;

### 2. 通过切点来选择连接点


### 3. 使用注解创建切面
使用注解来创建切面是AspectJ 5所引入的关键特性。AspectJ面向注解的模型可以非常简便地通过少量注解把任意类转变为切面。

```java
@Aspect
public class Audience {
    @Before("execution(** concert.Performance.perform(..))")
    public void silenceCellPhone() {
        System.out.println("Silence cell phones");
    }

    @Before("execution(** concert.Performance.perform(..))")
    public void takeSeats() {
        System.out.println("Taking seats");
    }

    @AfterReturning("execution(** concert.Performance.perform(..))")
    public void appluase() {
        System.out.println("CLAP CLAP CLAP!!!");
    }

    @AfterThrowing("execution(** concert.Performance.perform(..))")
    public void demandRefund() {
        System.out.println("Demanding a refund");
    }

}
```

Audience类使用`@AspectJ`注解进行了标注。该注解表明`Audience`不仅仅是一个POJO，还是一个切面。`Audience`类中的方法都使用注解来定义切面的具体行为。

AspectJ提供了五个注解来定义通知:
* `@After` 通知方法会在目标方法返回或抛出异常后调用
* `@AfterReturning` 通知方法会在目标方法返回后调用
* `@AfterThrowing` 通知方法会在目标方法抛出异常后调用
* `@Around` 通知方法会将目标方法封装起来
* `@Before` 通知方法会在目标方法调用之前执行

`@Pointcut`注解能够在一个`@AspectJ`切面内定义可重用的切点。
```java
@Aspect
public class Audience {

    //定义命名的切入点
    @Pointcut("execution(** concert.Performance.perform(..))")
    public void performance() {}

    @Before("performance()")
    public void silenceCellPhone() {
        System.out.println("Silence cell phones");
    }

    @Before("performance()")
    public void takeSeats() {
        System.out.println("Taking seats");
    }

    @AfterReturning("performance()")
    public void appluase() {
        System.out.println("CLAP CLAP CLAP!!!");
    }

    @AfterThrowing("performance()")
    public void demandRefund() {
        System.out.println("Demanding a refund");
    }

}
```

Spring的AspectJ自动代理仅仅使用`@AspectJ`作为创建切面的指导，切面依然是基于代理的。这一点非常重要，因为这意味着尽管使用的是`@AspectJ`注解，但我们仍然限于代理方法的调用。如果想利用AspectJ的所有能力，我们必须在运行时使用AspectJ并且不依赖Spring来创建基于代理的切面。



### 4. 在XML中声明切面

在Spring的aop命名空间中，提供了多个元素用来在XML中声明切面。 Spring的AOP配置元素能够以非侵入性的方式声明切面。

* 声明前置和后置通知
* 声明环绕通知
* 为通知传递参数
* 通过切面引入新的功能



### 5. 注入AspectJ切面

与AspectJ相比，Spring AOP 是一个功能比较弱的AOP解决方案。AspectJ提供了Spring AOP所不能支持的许多类型的切点。
可以借助Spring的依赖注入把bean装配进AspectJ切面中。
