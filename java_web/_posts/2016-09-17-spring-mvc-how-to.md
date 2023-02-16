---
title: spring mvc 入门
---

基于spring mvc的各种场景demo

主要参考自[http://o7planning.org/en/10129/spring-mvc-tutorial-for-beginners][2]

# 环境

- java 1.7
- maven 3.3.3
- spring 4.3.2.RELEASE
- intellij idea 15

# 添加jstl

pom.xml

    <!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>



# 在Intellij Idea创建基于maven的 web app

参考[http://qiudeqing.com/tools/2016/08/15/intellij-idea.html][1]完成项目创建, 本地运行.

# 集成spring mvc: hello world

1. 修改`pom.xml`添加spring依赖

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.qiudeqing</groupId>
            <artifactId>hello</artifactId>
            <packaging>war</packaging>
            <version>1.0-SNAPSHOT</version>
            <name>hello Maven Webapp</name>
            <url>http://maven.apache.org</url>

            <properties>
                <spring.version>4.3.2.RELEASE</spring.version>
                <servlet.version>3.1.0</servlet.version>

            </properties>

            <dependencies>

                <!-- spring begin -->
                <dependency>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-webmvc</artifactId>
                    <version>${spring.version}</version>
                </dependency>
                <!-- spring end -->

                <!-- servlet start -->
                <dependency>
                    <groupId>javax.servlet</groupId>
                    <artifactId>javax.servlet-api</artifactId>
                    <version>${servlet.version}</version>
                    <scope>provided</scope>
                </dependency>
                <!-- servlet end -->

            </dependencies>
            <build>
                <finalName>hello</finalName>
            </build>
        </project>


2. 添加`src/main/java/com/qiudeqing/controller/demo/HelloWorldController.java`

        package com.qiudeqing.controller.demo;

        import com.sun.tools.corba.se.idl.StringGen;
        import org.springframework.stereotype.Controller;
        import org.springframework.ui.Model;
        import org.springframework.web.bind.annotation.RequestMapping;

        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;

        /**
         * Created by qiudeqing on 16/9/17.
         */
        @Controller
        public class HelloWorldController {

            @RequestMapping("/demo/hello")
            public String hello(Model model) {
                model.addAttribute("greeting", "你好 Spring MVC");
                return "demo/hello";
            }
        }



3. 添加`src/main/resources/spring/spring-mvc-servlet.xml`

        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:p="http://www.springframework.org/schema/p"
               xmlns:context="http://www.springframework.org/schema/context"
               xmlns:mvc="http://www.springframework.org/schema/mvc"
               xsi:schemaLocation="http://www.springframework.org/schema/beans
             http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
             http://www.springframework.org/schema/context
             http://www.springframework.org/schema/context/spring-context-4.1.xsd
             http://www.springframework.org/schema/mvc
             http://www.springframework.org/schema/mvc/spring-mvc-4.1.xsd">

            <context:component-scan base-package="com.qiudeqing.controller" />

            <context:annotation-config />

            <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">

                <property name="prefix">
                    <value>/WEB-INF/jsps/</value>
                </property>

                <property name="suffix">
                    <value>.jsp</value>
                </property>

            </bean>

        </beans>

4. 添加`src/main/webapp/WEB-INF/jsps/demo/hello.jsp`

        <%@ page contentType="text/html;charset=UTF-8" language="java" %>
        <html>
        <head>
            <meta charset="utf-8">
            <title>Title</title>
        </head>
        <body>
        <h2>${greeting}</h2>
        </body>
        </html>

5. 配置`src/main/webapp/WEB-INF/web.xml`

        <?xml version="1.0" encoding="UTF-8"?>
        <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xmlns="http://java.sun.com/xml/ns/javaee"
                 xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
                 id="WebApp_ID" version="3.0">

            <servlet>
                <servlet-name>spring-mvc</servlet-name>
                <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
                <init-param>
                    <param-name>contextConfigLocation</param-name>
                    <param-value>/WEB-INF/classes/spring/*.xml</param-value>
                </init-param>
                <load-on-startup>1</load-on-startup>
            </servlet>

            <servlet-mapping>
                <servlet-name>spring-mvc</servlet-name>
                <url-pattern>/</url-pattern>
            </servlet-mapping>

         </web-app>

6. 重启服务器, 浏览器访问`http://localhost:8080/demo/hello`, 页面显示**你好Spring MVC**表示成功.



# 配置log4j为日志

参考文章[https://www.mkyong.com/spring-mvc/spring-mvc-log4j-integration-example/][]

1. 配置`pom.xml`, 添加log4j依赖

        <!-- log4j start -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <!-- log4j end -->

2. 添加`src/main/resources/log4j.properties`

        # Root logger option
        log4j.rootLogger=INFO, stdout, file

        # Redirect log messages to console
        log4j.appender.stdout=org.apache.log4j.ConsoleAppender
        log4j.appender.stdout.encoding=UTF-8
        log4j.appender.stdout.Target=System.out
        log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
        log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

        # Redirect log messages to a log file, support file rolling.
        log4j.appender.file=org.apache.log4j.RollingFileAppender
        log4j.appender.file.encoding=UTF-8
        log4j.appender.file.File=/Users/qiudeqing/Desktop/personal-projects/test.json
        log4j.appender.file.MaxFileSize=5MB
        log4j.appender.file.MaxBackupIndex=10
        log4j.appender.file.layout=org.apache.log4j.PatternLayout
        log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

3. 在action中使用logger

        package com.qiudeqing.controller.demo;

        import org.apache.log4j.Logger;
        import org.springframework.stereotype.Controller;


        /**
         * Created by qiudeqing on 16/9/17.
         */
        @Controller
        public class HelloWorldController {

            private static  final Logger logger = Logger.getLogger(HelloWorldController.class);

            @RequestMapping("/demo/log")
            public String log() {
                logger.error("错误信息");
                return "demo/hello";
            }

        }


# 访问HttpServletRequest和HttpServletResponse

    package com.qiudeqing.controller.demo;

    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    /**
     * Created by qiudeqing on 16/9/17.
     */
    @Controller
    public class HelloWorldController {

        @RequestMapping("/demo/raw")
        public String raw(HttpServletRequest request, HttpServletResponse response) {
            // do something
            return "demo/raw";
        }

    }

# 简单参数获取

    package com.qiudeqing.controller.demo;

    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    /**
     * Created by qiudeqing on 16/9/17.
     */
    @Controller
    public class HelloWorldController {

        @RequestMapping("/demo/simple-param")
        public String simpleParam(Model model, @RequestParam(value = "name", defaultValue = "Guest") String name) {
            model.addAttribute("name", name);
            return "demo/simple-param";
        }

    }

# restful接口

    package com.qiudeqing.controller.demo;

    import org.apache.log4j.Logger;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    /**
     * Created by qiudeqing on 16/9/17.
     */
    @Controller
    public class HelloWorldController {

        private static  final Logger logger = Logger.getLogger(HelloWorldController.class);

        @RequestMapping("/demo/book/{id}/{title}")
        public String book(Model model,
                           @PathVariable(value = "id") String id,
                           @PathVariable(value = "title") String title) {
            model.addAttribute("id", id);
            model.addAttribute("title", title);

            return "demo/book";
        }

    }

# 返回json


# The request sent by the client was syntactically incorrect

@PathVariable指定的参数名必须和url模板一致, 否则会报错

# 配置静态资源访问

    @Configuration
    @EnableWebMvc
    @ComponentScan(basePackageClasses = {HelloWorldController.class})
    public class WebConfiguration extends WebMvcConfigurerAdapter {

        /**
         * configure ResourceHandlers to serve static resources like css js
         * 所有/static/开头的请求都用webapp下/static/目录下的资源响应
         */
        @Override
        public void addResourceHandlers(ResourceHandlerRegistry registry) {
            registry.addResourceHandler("/static/**")
                    .addResourceLocations("/static");
        }
    }




[2]: http://o7planning.org/en/10129/spring-mvc-tutorial-for-beginners
[1]: http://qiudeqing.com/tools/2016/08/15/intellij-idea.html

