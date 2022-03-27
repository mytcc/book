---
layout: post
title:  如何使用Spring AOP进行切面编程?
categories: [markdown]
excerpt: 新建文章时或查看Markdown语法时可以参考此模板.
---
#### 概览
什么是面向切面编程？

面向切面编程是一种编程范式（其他常见的编程范式有 面向过程编程，面向对象编程OOP，面向函数编程，面向事件驱动编程，面向切面编程），它不是一种编程语言，面向切面编程可以解决特定的问题，但是不能解决所有问题，它是面向对象编程的补充，不是替代。

它可以很大程度上解决代码重复性问题，而且可以实现关注点分离，比如功能性需求和非功能性需求的分离，从而实现集中管理，增强代码的可读性，可维护性。

#### AOP常见的使用场景
在系统开发过程中常见的使用场景 主要有
- 权限控制
- 缓存控制
- 事务控制
- 审计日志
- 性能监控
- 分布式追踪
- 异常处理

#### Spring AOP 两个主要关注点

**Pointcut express**  
切面表达式，主要表达通过怎样的方式找到切面插入的逻辑点，pointcut express 提供了丰富的表达式可以让我们进行切面的插入。

**五种Advice**
找到切入点后，需要明确在什么时机进行代码植入，主要有五种，如下：

```java
@Before  //前置通知
@After(finally) //后置通知，在方法执行完之后切入
@AfterReturning //返回通知，返回值返回之后
@AfterThrowing //异常通知，抛出异常之后
@Around //环绕通知，环绕通知包含了上面所有的类型
```
以上两个关注点 总结一句话就是 在什么地方什么时机进行我们的代码切入

#### 常见切面表达式  
1. within表达式，匹配包或者类 下面的方法  

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * //匹配ProductService类里头的所有方法
 * @Pointcut("within(com.ruoli.service.ProductService)")
 * //匹配com.ruoli包及子包下所有类的方法
 * @Pointcut("within(com.ruoli..*)")
 */
@Aspect
@Component
public class PkgTypeAspectConfig {
   @Pointcut("within(com.ruoli.service.sub.*)")
   public void matchType(){}
   @Before("matchType()")
   public void before(){
       System.out.println("");
       System.out.println("###before");
   }
}
```
2. 对象匹配  

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * //匹配AOP对象的目标对象为指定类型的方法,即LogService的aop代理对象的方法
 * @Pointcut("this(com.ruoli.log.Loggable)")
 * //匹配实现Loggable接口的目标对象(而不是aop代理后的对象)的方法
 * @Pointcut("target(com.ruoli.log.Loggable)")
 * //this 可以拦截 DeclareParents(Introduction)
 * //target 不拦截 DeclareParents(Introduction)
 * //匹配所有以Service结尾的bean里头的方法
 * @Pointcut("bean(*Service)")
 * Created by cat on 2016-12-04.
 */
@Aspect
@Component
public class ObjectAspectConfig {

   @Pointcut("bean(logService)")
   public void matchCondition(){}

   @Before("matchCondition()")
   public void before(){
       System.out.println("");
       System.out.println("###before");
   }
}
```
3. 参数匹配，配置指定参数的方法   

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * //匹配任何以find开头而且只有一个Long参数的方法
 * @Pointcut("execution(* *..find*(Long))")
 * //匹配任何以find开头的而且第一个参数为Long型的方法
 * @Pointcut("execution(* *..find*(Long,..))")
 * //匹配任何只有一个Long参数的方法
 * @Pointcut("within(com.ruoli..*) && args(Long)")
 * //匹配第一个参数为Long型的方法
 * @Pointcut("within(com.ruoli..*) && args(Long,..)")
 * Created by cat on 2016-12-04.
 */
@Aspect
@Component
public class ArgsAspectConfig {
   @Pointcut("args(Long,String) && within(com.ruoli.service.*)")
   public void matchArgs(){}

   @Before("matchArgs()")
   public void before(){
       System.out.println("");
       System.out.println("###before");
   }
}
```

4. 注解匹配  
主要有 方法级别注解，类级别注解，参数级别注解。  

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * //匹配方法标注有AdminOnly的注解的方法
 * @Pointcut("@annotation(com.ruoli.anno.AdminOnly) && within(com.ruoli..*)")
 * //匹配标注有NeedSecured的类底下的方法 //class级别
 * @Pointcut("@within(com.ruoli.anno.NeedSecured) && within(com.ruoli..*)")
 * //匹配标注有NeedSecured的类及其子类的方法 //runtime级别
 * 在spring context的环境下,二者没有区别
 * @Pointcut("@target(com.ruoli.anno.NeedSecured) && within(com.ruoli..*)")
 * //匹配传入的参数类标注有Repository注解的方法
 * @Pointcut("@args(com.ruoli.anno.NeedSecured) && within(com.ruoli..*)")
 * Created by cat on 2016-12-04.
 */
@Aspect
@Component
public class AnnoAspectConfig {

   @Pointcut("@args(com.ruoli.anno.NeedSecured) && within(com.ruoli..*)")
   public void matchAnno(){}

   @Before("matchAnno()")
   public void before(){
       System.out.println("");
       System.out.println("###before");
   }

}
```
5. execution 表达式  

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * //匹配任何公共方法
 @Pointcut("execution(public * com.ruoli.service.*.*(..))")

 //匹配com.imooc包及子包下Service类中无参方法
 @Pointcut("execution(* com.ruoli..*Service.*())")

 //匹配com.imooc包及子包下Service类中的任何只有一个参数的方法
 @Pointcut("execution(* com.ruoli..*Service.*(*))")

 //匹配com.imooc包及子包下任何类的任何方法
 @Pointcut("execution(* com.ruoli..*.*(..))")

 //匹配com.imooc包及子包下返回值为String的任何方法
 @Pointcut("execution(String com.ruoli..*.*(..))")

 //匹配异常
 execution(public * com.ruoli.service.*.*(..) throws java.lang.IllegalAccessException)

 *
 */
@Aspect
@Component
public class ExecutionAspectConfig {

	@Pointcut("execution(public * com.ruoli.service..*Service.*(..) throws java.lang.IllegalAccessException)")
	public void matchCondition(){}

	@Before("matchCondition()")
	public void before(){
	 System.out.println("");
	 System.out.println("###before");
	}
}
```

6. 五种通知代码示例

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.omg.CORBA.Object;
import org.springframework.stereotype.Component;

/**
 * @Before("matchAnno()")
 * @After("matchAnno())") //相当于finally
 * @AfterReturning("matchException()")
 * @AfterThrowing("matchException()")
 * @Around("matchException()")
 * @Before(value = "matchLongArg() && args(productId)")
 * public void beforeWithArgs(Long productId)
 * @AfterReturning(value = "matchReturn()",returning = "returnValue")
 * public void getReulst(Object returnValue)
 *
 */
@Aspect
@Component
public class AdviceAspectConfig {

    /******pointcut********/

    @Pointcut("@annotation(com.ruoli.anno.AdminOnly) && within(com.ruoli..*)")
    public void matchAnno(){}

    @Pointcut("execution(* *..find*(Long)) && within(com.ruoli..*) ")
    public void matchLongArg(){}

    @Pointcut("execution(public * com.ruoli.service..*Service.*(..) throws java.lang.IllegalAccessException) && within(com.ruoli..*)")
    public void matchException(){}

    @Pointcut("execution(String com.ruoli..*.*(..)) && within(com.ruoli..*)")
    public void matchReturn(){}


    /******advice********/
    @Before("matchLongArg() && args(productId)")
    public void before(Long productId){
        System.out.println("###before,get args:"+productId);
    }
   @Around("matchException()")
   public java.lang.Object after(ProceedingJoinPoint joinPoint){
       System.out.println("###before");
       java.lang.Object result = null;
       try{
           result = joinPoint.proceed(joinPoint.getArgs());
           System.out.println("###after returning");
       }catch (Throwable e){
           System.out.println("###ex");
           //throw
           e.printStackTrace();
       }finally {
           System.out.println("###finally");
       }
       return result;
   }

}
```
