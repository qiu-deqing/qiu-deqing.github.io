---
title: spring mvc
---

# 根路径/总是访问`index.jsp`, 而不是`/`控制器


删掉`webapp/index.jsp`

# jsp中`${name}`输出`${name}`而不是name的值

原因: `web.xml` dtd使用的比较旧
解决方法: 换成高级版本

```
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
```

# form提交数据到后台是乱码

`web.xml`配置,添加过滤器

```
    <filter>
        <filter-name>CharacterEncoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```




# 参考资料

- [入门教程: 原理, demo简洁清晰][1]
- [https://crunchify.com/simplest-spring-mvc-hello-world-example-tutorial-spring-model-view-controller-tips/][2]
- [spring4 各种教程][3]

[3]: http://websystique.com/springmvc/spring-4-mvc-apache-tiles-3-annotation-based-example/
[2]: https://crunchify.com/simplest-spring-mvc-hello-world-example-tutorial-spring-model-view-controller-tips/
[1]: http://o7planning.org/en/10129/spring-mvc-tutorial-for-beginners
