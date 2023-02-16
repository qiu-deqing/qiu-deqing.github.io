---
title: spring Profile配置不同环境下的bean
---


开发环境和生产环境需要不同的配置, 如数据库, 使用Profile配置

# 如何设置profile

Spring按照顺序检查profile属性

- `spring.profiles.active`: 如果设置了值, spring会使用这个值作为profile创建对应Bean, 如果没有设置, 检查default设置
- `spring.profiles.default`: 如果设置了值, 使用这个值作为profile创建Bean, 如果没有设置, 创建没有设置profile的Bean

可以通过以下方法设置profile值

- `DispatcherServlet`的初始化参数传入
- web app上下文参数传入
- JNDI入口
- 环境变量
- JVM系统属性
- `@ActiveProfiles`

## 通过`web.xml`配置web app上下文参数

在`web.xml`中分别为`ContextLoaderListener`和`DispatcherServlet`设置`spring.profiles.default`为开发环境.
前面的开发环境数据库为Spring测试内部数据库, 开发人员可以直接运行项目, 不需要其他配置.

当部署到测试环境或者线上环境时, 通过系统属性, 环境变量, 或者JNDI等方法设置`spring.profiles.active`为环境值即可

这样就可以覆盖default值

具体的值可以是逗号分隔的多个值, 如`spring.profiles.default=dev,qa`


    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns="http://java.sun.com/xml/ns/javaee"
             xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
             id="WebApp_ID" version="3.0">

        <display-name>Spring mvc demo</display-name>

        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:/spring/root-context.xml</param-value>
        </context-param>
        <context-param>
            <param-name>spring.profiles.default</param-name>
            <param-value>dev</param-value>
        </context-param>

        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>

        <servlet>
            <servlet-name>spring-mvc</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:/spring/spring-mvc-servlet.xml</param-value>
            </init-param>
            <init-param>
                <param-name>spring.profiles.default</param-name>
                <param-value>dev</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>

        <servlet-mapping>
            <servlet-name>spring-mvc</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>


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

    </web-app>

## 使用`@ActiveProfiles`设置profile

junit运行集成测试的时候也需要设置profile

    package com.qiudeqing;

    import com.qiudeqing.dao.demo.UserDao;
    import junit.framework.Assert;
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.test.context.ActiveProfiles;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

    /**
     * Created by qiudeqing on 16/9/29.
     */
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = {SpringConfig.class})
    @ActiveProfiles("prod")
    public class ProfileTest {

        @Autowired
        private UserDao userDao;

        @Test
        public void testUserDao() {
            Assert.assertEquals(userDao.hello(), "userdao prod");
        }

        public UserDao getUserDao() {
            return userDao;
        }

        public void setUserDao(UserDao userDao) {
            this.userDao = userDao;
        }

    }


# java配置@Profile


在类级别使用@Profile或者方法级别使用

生产环境配置DevelopmentProfileConfig.java

    package com.qiudeqing.profile;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.Profile;
    import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
    import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;

    import javax.activation.DataSource;

    @Configuration
    @Profile("dev")
    public class DevelopmentProfileConfig {

        @Bean(destroyMethod= "shutdown")
        public DataSource dataSource() {
            return (DataSource) new EmbeddedDatabaseBuilder()
                    .setType(EmbeddedDatabaseType.H2)
                    .addScript("classpath:schema.sql")
                    .addScript("classpath:test-data.sql")
                    .build();
        }
    }


生产环境配置ProductionProfileConfig.java

    package com.qiudeqing.profile;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.Profile;
    import org.springframework.jndi.JndiObjectFactoryBean;

    import javax.activation.DataSource;

    /**
     * Created by qiudeqing on 16/9/28.
     */
    @Configuration
    @Profile("prod")
    public class ProductionProfileConfig {

        @Bean
        public DataSource dataSource() {
            JndiObjectFactoryBean jndiObjectFactoryBean =
                    new JndiObjectFactoryBean();
            jndiObjectFactoryBean.setJndiName("jdbc/myDS");
            jndiObjectFactoryBean.setResourceRef(true);
            jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
            return (DataSource)jndiObjectFactoryBean.getObject();
        }
    }


# xml配置profile属性

datasource-dev.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:jdbc="http://www.springframework.org/schema/jdbc"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/jdbc
                http://www.springframework.org/schema/jdbc/spring-jdbc.xsd"
                profile="dev">


        <jdbc:embedded-database id="dataSource">
            <jdbc:script location="classpath:schema.sql"/>
            <jdbc:script location="classpath:test-data.sql"/>
        </jdbc:embedded-database>

    </beans>

datasource-prod.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:jdbc="http://www.springframework.org/schema/jdbc"
           xmlns:jee="http://www.springframework.org/schema/jee"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/jdbc
                http://www.springframework.org/schema/jdbc/spring-jdbc.xsd"
           profile="prod">


        <jee:jndi-lookup jndi-name="jdbc/myDatabase"
                         id="dataSource"
                         resource-ref="true"
                         proxy-interface="javax.sql.DataSource"
            />

    </beans>
