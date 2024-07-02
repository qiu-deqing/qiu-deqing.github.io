---
title: spring aop
---

日志, 安全, 事物管理, 缓存这样多个模块都需要的功能应该抽离出来, Spring aop就是为这个工作准备.

AOP允许模块不进行任何修改的情况下添加所需功能.

# 术语

![][1]

- advice: 切面所做工作叫做advice

    advice定义了切面工作的**what**和**when**, 包含以下五种:

    - **Before**: 切面功能在目标方法之前执行
    - **After**: 切面功能在目标方法之后执行
    - **After-returning**: 切面功能在目标方法完全完成之后执行
    - **After-throwing**: 切面功能在目标方法抛出异常之后执行
    - **Around**: 切面功能在目标功能前后都执行

- join point: 应用程序可以试用切面的地方. 可以是被调用的方法, 抛出的异常, 字段的修改.
- pointcuts: 切面应用的地方, 定义一个或多个执行切面的join point
- aspects: advice和pointcuts的集合, 确定什么时候在哪里做什么
- introductions: 一个introduction允许你为现有类添加属性和方法. 例如你创建了Auditable advice类来保存对象最后修改时间. 具有一个`setLastModified(Date)`, 一个属性保存这个状态. 这个方法和属性可以在不修改现有类的情况下被添加.
- weaving: 向目标对象应用切面创建代理对象的过程. weaving可以发生在目标对象生命周期的以下时候:

    - compile time: 目标对象编译时, 这需要特定的编译器, AspectJ的weaving编译器在这个阶段完成任务.
    - class load time:  目标对象加载到JVM的时候. 这需要特定的ClassLoader在加载类加载到应用程序之前修改byte-code. AspectJ 5的load-time weaving能支持这个功能
    - runtime: 程序执行时执行时生效. 通常由AOP容器动态生成代理对象. Spring AOP就是使用这种方式.

# Spring AOP概述

Spring AOP有以下形式

- 基于代理的经典Spring AOP(过时且繁琐)
- 纯POJO切面
- @AspectJ注解驱动
- 注入AspectJ切面(所有版本Spring都支持)

Spring AOP基于动态代理并且限制范围为方法拦截.

通过Spring的`aop`命名空间, 你可以将POJO转化为切面. POJO只能提供方法支持. 必须使用XML配置.

如果你的AOP需求超过了简单的方法拦截(例如构造函数或者属性拦截), 你需要实现AspectJ切面. 也就是上面的第四种方法.


# 实现AOP

## 1. `pom.xml`添加依赖

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <groupId>com.qiudeqing</groupId>
      <artifactId>spring-aop</artifactId>
      <packaging>war</packaging>
      <version>1.0-SNAPSHOT</version>
      <name>spring-aop Maven Webapp</name>
      <url>http://maven.apache.org</url>

        <properties>
            <spring.version>4.1.7.RELEASE</spring.version>
            <junit.version>4.12</junit.version>

        </properties>

        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>aspectj</groupId>
                <artifactId>aspectjrt</artifactId>
                <version>1.5.4</version>
            </dependency>
            <dependency>
                <groupId>aspectj</groupId>
                <artifactId>aspectjweaver</artifactId>
                <version>1.5.4</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
      <build>
        <finalName>spring-aop</finalName>
      </build>
    </project>



## 2. 定义正常业务逻辑

`SimpleService`接口

    package com.qiudeqing.service;

    /**
     * Created by qiudeqing on 16/10/14.
     */
    public interface SimpleService {
        public void printNameId();
        public void checkName();
        public String sayHello(String message);
    }



`SimpleServiceImpl`实现

    package com.qiudeqing.service;

    /**
     * Created by qiudeqing on 16/10/14.
     */
    public class SimpleServiceImpl implements SimpleService {
        private String name;
        private int id;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getId() {
            return id;
        }

        public void setId(int id) {
            this.id = id;
        }

        public void printNameId() {
            System.out.println("SimpleService :method printNamedId(): My name is " + name + " my id is " + id);
        }

        public void checkName() {
            if (name.length() < 20) {
                throw new IllegalArgumentException();
            }
        }

        public String sayHello(String message) {
            System.out.println("SimpleService : Method sayHello() : Hello ! " + message);
            return message;
        }
    }


# 3. 创建各种所需的aspect

使用`AspectJ`提供的各种注解拦截各种业务方法, `@Aspect`注解的类叫做aspect, aspect包含pointcut, advice,
introduction声明.

有一下五种类型的advice:

- @Before
- @After
- @AfterReturning
- @AfterThrowing
- @Around

## 3.1 @Before

设置advice为`@Before`将会在join point之前执行Aspect

    package com.qiudeqing.aspect;

    import org.aspectj.lang.JoinPoint;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;

    /**
     * Created by qiudeqing on 16/10/13.
     */
    @Aspect
    public class DoBeforeAspect {

        @Before("execution(* com.qiudeqing.service.SimpleService.sayHello(..))")
        public void doBefore(JoinPoint joinPoint) {
            System.out.println("***AspectJ*** DoBefore() is running!! interceped: " + joinPoint.getSignature().getName());
        }
    }

在`applicationContext.xml`声明aspect bean并且启用`aop:aspectj-autoproxy`

    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:p="http://www.springframework.org/schema/p"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:jee="http://www.springframework.org/schema/jee"
           xmlns:tx="http://www.springframework.org/schema/tx"
           xmlns:task="http://www.springframework.org/schema/task"
           xsi:schemaLocation="http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop-3.2.xsd
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context-3.2.xsd
            http://www.springframework.org/schema/jee
            http://www.springframework.org/schema/jee/spring-jee-3.2.xsd
            http://www.springframework.org/schema/tx
            http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
            http://www.springframework.org/schema/task
            http://www.springframework.org/schema/task/spring-task-3.2.xsd">

        <aop:aspectj-autoproxy />

        <bean id="simpleServiceBean" class="com.qiudeqing.service.SimpleServiceImpl">
            <property name="name" value="Hello" />
            <property name="id" value="12345" />
        </bean>

        <bean id="doBeforeAspect" class="com.qiudeqing.aspect.DoBeforeAspect" />

    </beans>

添加junit测试, 可以看到`sayHello()`执行前执行了doBefore Aspect

    package com.qiudeqing.aspect;

    import com.qiudeqing.service.SimpleService;
    import org.junit.Test;
    import org.springframework.context.ConfigurableApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    /**
     * Created by qiudeqing on 16/10/14.
     */
    public class DoBeforeTest {

        @Test
        public void doBeforeTest() {
            ConfigurableApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
            SimpleService simpleService = (SimpleService)context.getBean("simpleServiceBean");
            simpleService.printNameId();
            System.out.println("---------------------------");
            try {
                simpleService.checkName();
            } catch (Exception e) {
                System.out.println("SimpleService checkName() : Exception thrown..");
            }
            System.out.println("-----------------");
            simpleService.sayHello("javacodegeeks");
            System.out.println("---------------");
            context.close();
        }

    }

结果:

    SimpleService :method printNamedId(): My name is Hello my id is 12345
    ---------------------------
    SimpleService checkName() : Exception thrown..
    -----------------
    ***AspectJ*** DoBefore() is running!! interceped: sayHello
    SimpleService : Method sayHello() : Hello ! javacodegeeks
    ---------------


## 3.2 @After

@After类型的advice将在join point执行之后执行. 例如: 如果join point是函数执行, @After advice会在方法返回后或者抛出异常后执行.

DoAfterAspect.java

    package com.qiudeqing.aspect;

    import org.aspectj.lang.JoinPoint;
    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.Aspect;

    /**
     * Created by qiudeqing on 16/10/14.
     */
    @Aspect
    public class DoAfterAspect {

        @After("execution(* com.qiudeqing.service.SimpleService.sayHello(..))")
        public void doAfter(JoinPoint joinPoint) {
            System.out.println("***AspectJ*** DoAfter() is running!! interceped: " + joinPoint.getSignature().getName());
        }
    }


applicationContext.xml中添加bean配置


    <bean id="doAfterAspect" class="com.qiudeqing.aspect.DoAfterAspect" />


此时再次运行前面的doBeforeTest会自动执行doAfterAspect配置的方法.

执行结果:

    SimpleService :method printNamedId(): My name is Hello my id is 12345
    ---------------------------
    SimpleService checkName() : Exception thrown..
    -----------------
    ***AspectJ*** DoBefore() is running!! interceped: sayHello
    SimpleService : Method sayHello() : Hello ! javacodegeeks
    ***AspectJ*** DoAfter() is running!! interceped: sayHello
    ---------------

# 3.3 @AfterReturning

@AfterReturn类型的aspect会在方法返回值之后执行, aspect可以获取方法返回值

DoAfterReturningAspect.java

    package com.qiudeqing.aspect;

    import org.aspectj.lang.JoinPoint;
    import org.aspectj.lang.annotation.AfterReturning;
    import org.aspectj.lang.annotation.Aspect;

    /**
     * Created by qiudeqing on 16/10/14.
     */
    @Aspect
    public class DoAfterReturningAspect {

        @AfterReturning(pointcut = "execution(* com.qiudeqing.service.SimpleService.sayHello(..))", returning = "result")
        public void doAfterReturning(JoinPoint joinPoint, Object result) {
            System.out.println("***AspectJ*** DoAfterReturning() is running!! intercepted : " + joinPoint.getSignature().getName());
            System.out.println("Method returned value is : " + result);
        }
    }

applicationContext.xml配置:


    <bean id="doAfterReturningAspect" class="com.qiudeqing.aspect.DoAfterReturningAspect" />

再次运行doBeforeText会自动在函数返回之后执行aspect:

    SimpleService :method printNamedId(): My name is Hello my id is 12345
    ---------------------------
    SimpleService checkName() : Exception thrown..
    -----------------
    ***AspectJ*** DoBefore() is running!! interceped: sayHello
    SimpleService : Method sayHello() : Hello ! javacodegeeks
    ***AspectJ*** DoAfterReturning() is running!! intercepted : sayHello
    Method returned value is : javacodegeeks
    ***AspectJ*** DoAfter() is running!! interceped: sayHello
    ---------------

## 3.4 @AfterThrowing

@AfterThrowing类型的Aspect在方法抛出异常时执行.

DoAfterThrowingAspect.java

    package com.qiudeqing.aspect;

    import org.aspectj.lang.JoinPoint;
    import org.aspectj.lang.annotation.AfterThrowing;
    import org.aspectj.lang.annotation.Aspect;

    /**
     * Created by qiudeqing on 16/10/18.
     */
    @Aspect
    public class DoAfterThrowingAspect {

        @AfterThrowing(
                pointcut = "execution(* com.qiudeqing.service.SimpleService.checkName(..))",
                throwing = "error"
        )
        public void doAfterThrowing(JoinPoint joinPoint, Throwable error) {
            System.out.println("***AspectJ*** DoAfterThrowing() is running!! intercepted : " + joinPoint.getSignature().getName());
            System.out.println("Exception : " + error);
            System.out.println("******");
        }
    }


applicationContext.xml配置

    <bean id="doAfterThrowingAspect" class="com.qiudeqing.aspect.DoAfterThrowingAspect" />


再次运行结果:

    SimpleService :method printNamedId(): My name is Hello my id is 12345
    ---------------------------
    ***AspectJ*** DoAfterThrowing() is running!! intercepted : checkName
    Exception : java.lang.IllegalArgumentException
    ******
    SimpleService checkName() : Exception thrown..
    -----------------
    ***AspectJ*** DoBefore() is running!! interceped: sayHello
    SimpleService : Method sayHello() : Hello ! javacodegeeks
    ***AspectJ*** DoAfterReturning() is running!! intercepted : sayHello
    Method returned value is : javacodegeeks
    ***AspectJ*** DoAfter() is running!! interceped: sayHello
    ---------------

## 3.5 @Around

环绕通知(Around Advice)是包围一个连接点的通知,如方法调用.这是最强大的一种通知类型. 环绕通知可以再方法调用前后完成自定义的行为.

它也会选择是否继续执行连接点或直接返回它自己的返回值或抛出异常来结束执行.

环绕通知时最常用的通知类型, 和AspectJ一样, S热评提供所有类型的通知, 我们推荐使用尽可能简单的通知类型来实现所需功能.例如你只是需要一个方法的返回值来更新焕春, 最好使用后置通知而不是环绕通知.

DoAroundAspect.java

    package com.qiudeqing.aspect;

    import org.aspectj.lang.ProceedingJoinPoint;
    import org.aspectj.lang.annotation.Around;
    import org.aspectj.lang.annotation.Aspect;

    import java.util.Arrays;


    /**
     * Created by qiudeqing on 16/10/18.
     */
    @Aspect
    public class DoAroundAspect {
        @Around("execution(* com.qiudeqing.service.SimpleService.sayHello(..))")
        public void doAround(ProceedingJoinPoint joinPoint) throws Throwable {
            System.out.println("***AspectJ*** DoAround() is running!! interceped : " + joinPoint.getSignature().getName() +
                " \narguments : " + Arrays.toString(joinPoint.getArgs()));
            System.out.println("***AspectJ*** DoAround() before is running!");
            joinPoint.proceed();
            System.out.println("***AspectJ*** DoAround() after is running!");
        }
    }

applicationContext.xml

    <bean id="doAroundAspect" class="com.qiudeqing.aspect.DoAroundAspect" />

运行结果:

    SimpleService :method printNamedId(): My name is Hello my id is 12345
    ---------------------------
    ***AspectJ*** DoAfterThrowing() is running!! intercepted : checkName
    Exception : java.lang.IllegalArgumentException
    ******
    SimpleService checkName() : Exception thrown..
    -----------------
    ***AspectJ*** DoBefore() is running!! interceped: sayHello
    ***AspectJ*** DoAround() is running!! interceped : sayHello
    arguments : [javacodegeeks]
    ***AspectJ*** DoAround() before is running!
    SimpleService : Method sayHello() : Hello ! javacodegeeks
    ***AspectJ*** DoAround() after is running!
    ***AspectJ*** DoAfterReturning() is running!! intercepted : sayHello
    Method returned value is : null
    ***AspectJ*** DoAfter() is running!! interceped: sayHello
    ---------------


# 参考资料

- [https://examples.javacodegeeks.com/enterprise-java/spring/aop/spring-aop-aspectj-example/][2]
- [Spring AOP基本概念和配置详解][3]

[3]: http://blog.csdn.net/csh624366188/article/details/7651702
[2]: https://examples.javacodegeeks.com/enterprise-java/spring/aop/spring-aop-aspectj-example/
[1]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20161002-0.png
