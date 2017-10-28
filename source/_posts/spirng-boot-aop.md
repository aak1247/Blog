title: Spring Boot配置AOP实践

category: java web

tags: 
 - java
 - spring
 - spring boot
 - aop

date: 2017/10/28

---

# 关于AOP

## AOP的应用场景

&nbsp;&nbsp;&nbsp;&nbsp;在许多系统中都会有一些公共的行为或者逻辑，如果分散在各处一则重复代码很不优美，二则会给修改维护带来一些问题。将这些公共的行为抽离出来作为单独的可重用组件供需要的其他组件进行调用可以很有效地解决上述问题，而抽离出来的组件就是一个切面（``Aspect``）。


<!--more-->
## 一些概念

1. 通知（增强）Advice
    - 定义了切面的行为逻辑以及它什么时候使用。比如在切点之前、之后、抛出异常时。
    - 通知的类型：
        > 1. Before：前置通知，在切点之前调用通知（执行通知定义的行为）
        > 1. After：后置最终通知，在切点之后调用通知，无论切点所对应的方法执行成功与否
        > 1. After-returning：后置返回通知，在切点对应方法执行成功之后调用通知
        > 1. After-throwing：后置异常通知，在切点指定方法抛出异常后进行通知
        > 1. Around：环绕通知，通知包裹了被通知的切点方法，在被通知的方法调用之前和调用之后执行自定义的行为
2. 连接点 Join point
    - 连接点是在应用执行过程中能够插入切面的点。
    - 连接点是切面的调用者。
    - 切面在连接点处插入到运行流程当中。
3. 切点 Pointcut
    - 切点用于确定通知执行的时间，也就是通知与连接点的关系，如指定某些方法（连接点）为通知所匹配的切点，则该通知就围绕这些方法执行。
    - 切点会匹配通知所要织入的一个或者多个连接点。
    - 
4. 切面 Aspect
    - 切面是通知和切点的集合，通知和切点共同定义了切面的全部功能。
    - 可以理解为包含切点和通知的类。
5. 引入 Introduction
    - 引入允许我们向现有的类中添加方法或属性。
6. 织入 Weave
    - 织入是将切面应用到目标对象来创建的代理对象过程。
    - 切面在指定的连接点被织入到目标对象中，在目标对象的生命周期中有多个点可以织入:
        > 1. 编译期——切面在目标类编译时期被织入，这种方式需要特殊编译器。AspectJ的织入编译器就是以这种方式织入切面。
        > 1. 类加载期——切面在类加载到JVM ，这种方式需要特殊的类加载器，他可以在目标类被引入应用之前增强该目标类的字节码。AspectJ5 的 LTW 就支持这种织入方式
        > 1. 运行期——切面在应用运行期间的某个时刻被织入。一般情况下，在织入切面时候，AOP 容器会为目标对象动态的创建代理对象。Spring AOP 就是以这种方式织入切面。
7. 代理 Proxy
    - 实现AOP所使用的机制，可以分为动态代理和静态代理。
8. 横切关注点 Cross-cuting Concerns
    - 即影响应用多处的功能，如安全、事务、日志等，将这些横切关注点抽离出来变成公共的组件就得到切面。

## 原理简介

spring中使用动态代理实现AOP。

# spring boot中aop的配置

## 引入依赖

如果使用 ``maven`` 在 ``dependencies`` 中添加文件依赖即可，其他请自行修改。

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-aop</artifactId>  
</dependency>  
```

## 建立一个切面

```java
@Component  
@Aspect  
public class TestAop {  
  
}  
```

## 创建切点

在 ``TestAop`` 中建立切点方法。

```java
@Pointcut("execution(* com.aak1247.testaop.controller..*.*(..))")  
public void executeService(){  
  
} 
```

这个切点匹配 ``com.aak1247.testaop.controller`` 包下的所有类的所有方法（作为连接点）。

## 创建通知

在 ``TestAop`` 中建立通知方法，这里以前置通知（@Before）为例。

```java
@Before("executeService()")  
public void doBeforeAdvice(JoinPoint joinPoint){  
    System.out.println("调用前置通知");    
    Object[] obj = joinPoint.getArgs();  //获取目标方法的参数信息
    joinPoint.getThis();      //AOP代理类的信息 
    joinPoint.getTarget();      //代理的目标对象  
    Signature signature = joinPoint.getSignature();      //通知的签名  
    System.out.println(signature.getName());      //代理的方法名
    System.out.println(signature.getDeclaringTypeName());  //AOP代理类名字  
    System.out.println(signature.getDeclaringType());      //AOP代理类的类（class）信息  
}  
```

- 如果是后置最终通知，其参数与前置通知相同。
- 而后置返回通知的参数可以为 ``JointPoint`` 和返回对象，也可以仅仅为返回对象，如``Object para``。
- 后置异常通知的参数类型为``JointPoint``和``Throwable``。
- 环绕通知必须包含一个``org.aspectj.lang.ProceedingJoinPoint``类型参数。

> 在web环境下可以通过``RequestContextHolder``获取请求信息。
## 创建连接点

在``com.aak1247.testaop.controller``包下建立类和方法，作为前置通知的连接点。
```java
@RestController
public Class testController{
    @RequestMapping("/test")
    public String testBeforeService(){
        return "test controller";
    }
}
```

emmmm....结果自测好伐？

# 附录

## ``AspectJ``风格的切点表达式

应该注意到，在``@execution``注解中，括号里的内容可以通过一个模式匹配多个方法（连接点）。

``@exexution(a b.c(..))``
- a 确定了匹配到的方法的类型（public/private/protected/*）
- b 确定包名和类名，（可以是接口名），可以是包含通配符``*``的模式
- c 确定了方法名，可以是包含通配符``*``的模式
- 一个比较特殊的用法，比如如果指定c为``*``，而b为一个接口，就可以匹配实现了接口方法的所有方法

> 除此之外，``@within``，``@target``，``@args``等注解也可以用来匹配连接点。