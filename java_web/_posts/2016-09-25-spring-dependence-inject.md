---
title: spring依赖注入
---

# 隐式bean扫面和自动封装

核心工作包含两个方面:

- 组件扫描
- 自动封装

1. Bean设置`@Component`注解

        package com.qiudeqing;

        import org.springframework.stereotype.Component;


        @Component
        // @Component("beanName")
        public class SgtPeppers implements CompactDisc {

            public void play() {
                System.out.println("hello");
            }

        }


2. 新建配置类, 设置@Configuration标记为配置类, 设置@ComponentScan包路径

        package com.qiudeqing;

        import org.springframework.context.annotation.ComponentScan;
        import org.springframework.context.annotation.Configuration;

        @Configuration
        @ComponentScan
        // @ComponentScan(basePackages = {"com.qiudeqing"})
        // @ComponentScan(basePackageClasses = {CDPlayer.class, DVDPlayer.class}) 推荐
        public class CDConfig {
        }

3. 为Bean自动注入所依赖的类

        package com.qiudeqing;

        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.stereotype.Component;

        @Component
        public class CDPlayer implements MediaPlayer {
            private CompactDisc cd;

            @Autowired(required = false)
            public CDPlayer(CompactDisc cd) {
                this.cd = cd;
            }

            public void play() {
                cd.play();
            }
        }



# java显式配置

1. Bean配置和之前一样, 添加@Component

2. 和自动配置一样添加配置类, 设置@Configuration注解, 不设置@ComponentScan, 添加@Bean方法


        package com.qiudeqing;

        import org.springframework.context.annotation.ComponentScan;
        import org.springframework.context.annotation.Configuration;

        @Configuration
        public class CDConfig {

            @Bean(name = "beanName")
            public CompactDisc compactDisc() {
                return new SgtPeppers();
            }
        }

3. 为Bean注入依赖Bean


        @Bean CDPlayer cdPlayer(CompactDisc compactDisc) {
            return new CDPlayer(compactDisc)
        }


# xml显式配置

1. 编写普通java类, 在classpath下添加配置文件如`applicationContext.xml`

        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://www.springframework.org/schema/beans
                    http://www.springframework.org/schema/beans/spring-beans.xsd
                    http://www.springframework.org/schema/context">
            // bean 配置
        </beans>

2. 添加普通Bean

        <bean class="com.qiudeqing.SgtPeppers" />

3. 通过构造函数传递Bean, value

        <bean class="com.qiudeqing.Book" id="helloBook">
            <constructor-arg name="title" value="hello"/>
            <constructor-arg ref="compactDisc"/>
        </bean>

4. 通过属性注入Bean依赖

        <bean class="com.qiudeqing.Book" id="helloBook">
            <property name="compactDisc" ref="compactDisc" />
            <property name="title" value="hello" />
        </bean>


# java配置文件中引用其他java配置

@Configuration
@Import({CDConfig.class, HelloConfig.class})
public class CDPlayerConfig {
    @Bean
    public CDPlayer cdPlayer(CompactDisc compactDisc) {
        return new CDPlayer(compactDisc);
    }
}

# java配置文件中引入xml配置文件

@Configuration
@import(CDPlayerConfig.class)
@inportResource("classpath:cd-config.xml")
public class SoundSystemConfig {

}

# xml配置文件中引入xml配置文件

    <import resource="cd-config.xml" />

# xml配置文件中引入java配置

只需要添加配置文件所在java文件为bean即可

    <bean class="com.qiudeqing.CDConfig" />

# 设置根配置方便管理

不管是使用java类还是xml配置, 设置一个根配置文件, 在里面引入各个模块配置, 包扫描路径.


# Bean冲突

多个Bean类型满足相同类型时, 自动装配会产生冲突如下

    @Autowired
    public void setDessert(Dessert dessert) {
        this.dessert = dessert;
    }

其中接口Dessert有三个实现

    @Component
    public class Cake implements Dessert {}

    @Component
    public class Cookies implements Dessert {}

    @Component
    public class IceCream implements Dessert {}

## 设置一个主Bean

多个Bean满足条件时, Spring自动使用主Bean注入

    @Component
    @Primary
    public class IceCream implements Dessert {}


java配置设置

    @Bean
    @Primary
    public Dessert iceCream() {
        return new IceCream();
    }

xml配置

    <bean id="iceCream" class="IceCream" primary="true" />

## 限定自动装配Bean

主Bean不允许存在多个.

    @Autowired
    @Qualifier("iceCream")
    public void setDessert(Dessert dessert) {
        this.dessert = dessert;
    }

其中@Qualifier内参数指定寻找限定符为iceCream的类, 如果@Component没有设置限定名, 默认值为类名首字母小写
匹配类名首字母小写后的类. 上面的设置会寻找`IceCream`

依赖默认限定符存在潜在问题: 当类名修改时系统会找不到Bean, 可以给Bean显式设置限定符解决这个问题

    @Component
    @Qualifier("cold")
    public class IceCream implements Dessert {}

# Bean类型(作用域)

默认Bean都是单例类型, 所有对象共享一个Bean. 但是有的Bean不能共享. Spring提供以下类型的Bean.

- `Singleton`: 单例, 整个程序共享一个Bean, 这是默认类型
- `Progotype`: 原型, Bean每次注入或者从context获取的时候都新建一个
- `Session`:  会话, 在web app中, 一个会话共享一个bean
- `Request`: 请求, 在web app中每次请求都新建一个bean


通过@Scope配置类型

    @Component
    @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public class Notepad {}


java配置

    @Bean
    @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public Notepad notepad() {
        return new Notepad();
    }

xml配置

    <bean id="notepad" class="Notepad" scope="prototype" />

## 配置Bean类型为session

    @Component
    @Scope(
            value = WebApplicationContext.SCOPE_SESSION,
            proxyMode = ScopedProxyMode.INTERFACES
    )
    public class ShoppingCartImp implements ShoppingCart {
    }

上面的proxyMode是为了解决session或者request类型bean注入到singleton类型Bean引发的问题: 假设我们存在一个StoreService

    @Component
    public class StoreService {

        @Autowired
        public void setShoppingCart(ShoppingCart shoppingCart) {
            this.shoppingCart = shoppingCart;
        }
    }

StoreService是单例类型, 系统启动时Spring会创建它, 但是ShoppingCart是session类型, 必须用户访问时才能创建, 此时
就会出现问题. 当多个用户访问系统时选择ShoppingCart的注入也是问题. 所以Spring在系统初始化时给StoreService注入了
一个代理对象. 这个代理对象和提供ShoppingCart的全部接口, StoreService调用ShoppingCart方法时代理托管到真正对应的Bean.

proxyMode设置的值是`ScopedProxyMode.INTERFACES`, 表示代理对象必须实现ShoppingCart接口的方法. 如果ShoppingCart不是接口而是类, 需要设置`proxyMode = ScopedProxyMode.TARGET_CLASS`, springshiyong CGLib创建对应类实例对象.

在xml配置文件中:

    <bean id="cart"
        class="ShoppingCart"
        scope="session">
        <aop:scoped-proxy />
    </bean>

`<aop:scoped-proxy>`是@Scope proxyMode的对应属性. 默认采用CGLib创建类代理. 如果要创建基于接口的代理需要设置`proxy-target-class = "falst"`

    <bean id="cart"
        class="ShoppingCart"
        scope="session">
        <aop:scoped-proxy proxy-target-class="false" />
    </bean>

使用`<aop:scoped-proxy>`需要声明aop namespace


# 运行时值注入

spring提供两种机制支持运行时注入

- property placeholders
- spring expression language

## 注入外部值

解析外部值最简单的方法是定义一个属性源然后通过`Environment`获取属性值.


@Configuration
@PropertySource("classpath:user.properties")
public class SpringConfig {

    @Autowired
    Environment env;

    @Bean
    public User user() {
        return new User(
                env.getProperty("name")
        );
    }

}

## 通过属性占位符解析外部属性

    <bean id="sgtPeppers" class="User">
        <property name="name" value="${name}"/>
    </bean>

## Spring Expression Language

    public BlankDisc(
            @Value("#{systemProperties['disc.title']}") String title,
            @Value("#{systemProperties['disc.artist']}") String artist
        ) {
        this.title = title;
        this.artist = artist;
    }

    <bean id="sgtPeppers" class="BlankDisc">
        <property name="title" value="#{systemProperteis['disc.title']}"/>
    </bean>

### 1. 字面量

    #{3.14159}
    #{9.87E4}
    #{'Hello'}
    #{false}

### 2. 引用bean, 属性, 方法

通过id引用bean

    #{sgtPeppers}
    #{sgtPeppers.artist}
    #{artistSelector.selectArtist()}
    #{artistSelector.selectArtist().toUpperCase()}
    #{aritstSelector.selectArtist()?.toUpperCase()}     // 防止NullPointerException

### 3. 使用类型

使用`T()`操作符返回Class对象. 这样就可以访问值对应类的静态方法和常量.

    `T(java.lang.Math).PI`      // 访问java.lang.Math.PI
    `T(java.lang.Math).random()`        // 访问java.lang.Math.random()

### 4. 操作符

|    操作符类型   |  操作符   |
|---------------|-----------|
|  运算         | +, -, *, /, %, ^ |
|  比较         | <, lt, >, gt, ==, eq, <=, le, >=, ge |
|  逻辑         | and, or, not,  |
|  条件         | ?:(ternary), ?:(Elvis) |
|  正则         |  matches  |


    #{2 * T(java.lang.Math).PI * circle.radius}
    #{T(java.lang.Math).PI * circle.radius ^ 2}
    #{disc.title + ' by ' + disc.artist}
    #{counter.total == 100}
    #{scoreboard.score > 1000 ? "Winner!" : "Loser"}
    #{disc.title ?: 'Rattle and Hum'}
    #{admin.email matches '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]\\.com'}

### 5. 集合

    #{jukebox.songs[4].title}
    #{jukebox.songs[T(java.lang.Math).random() * jukebox.songs.size()].title}
    #{'This is a test'[3]}      // 第四个字母
    #{jukebox.songs.?[artist eq 'Aerosmith']}   // .?[] 过滤集合: 选择使括号内表达式为真的元素
    #{jukebox.songs.^[artist eq 'Aerosmith']}   // .&[] 选择第一个使括号内表达式为真的元素
    #{jukebox.songs.$[artist eq 'Aerosmith']}   // .$[] 选择最后一个使括号内表达式为真的元素
    #{jukebox.songs.![title]}           // 类似map: 映射
