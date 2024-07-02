---
title: spring boot
---

基于maven的spring boot web项目

# 常用命令

- `mvn spring-boot:run`: 启动基于spring boot web的项目
- `mvn build`编译,然后进入到`target`目录执行`java -jar xxx.jar`

# 属性配置

`application.properties`是配置文件也可以使用`application.yml`方便配置


# 在配置文件中设置的值在代码中访问方式

1. 直接访问

  ```
  @Value("${name}")
  private String name;
  ```

2. 复杂对象访问

  ```
  girl:
    cupSize: b
    age: 18
  ```

  添加对应Java类如`GirldProperties`

  ```

  @Component
  @ConfigurationProperties(prefix = "girl")
  public class GirlProperties {
      private String cupSize;
      private Integer age;
  }

  // 使用
  @Autowired
  private GirlProperties girlProperties;  // spring自动将配置文件中girl下面的属性读取设置到对象
  ```

# 配置不同开发环境

1. 根据需求编写多个配置文件,如`application-dev.yml`,`application-prod.yml`,在`application.yml`中设置生效的文件:
  ```
  spring:
    profiles:
      active: dev
  ```
  此时启动应用使用的配置文件是`application-dev.yml`

2. 通过命令行参数在应用启动时指定环境:`java -jar target/crm-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod`此时使用的配置文件是`application-prod.yml`


# spring boot热部署

1. maven添加依赖
  ```
  <!-- 热部署 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
  </dependency>
  ```
2. 设置idea允许自动编译`Cmd + shift + a`,输入`make project automatically`回车,勾选`Make Project automatically`
3. 允许idea在运行时编译`Cmd + shift + a`,输入`Registry`回车,勾选`compiler.automake.allow.when.running`
4. 通过idea启动或者`mvn spring-boot:run`启动服务器,修改代码后都会自动编译,热部署生效

# 以jar发布

1. `mvn install`编译打包为jar
2. `java -jar xxx.jar`启动

# 单元测试


# 添加mybatis

1. `pom.xml`添加 依赖

    ```
    <!-- Spring Boot Mybatis -->
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>${mybatis-spring-boot}</version>
    </dependency>

    <!-- MySQL 驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql-connector}</version>
    </dependency>
    ```

# mybatis generator插件

1. `pom.xml`添加plugin

    ```
    <plugin>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-maven-plugin</artifactId>
              <version>1.3.2</version>
      <configuration>
        <verbose>true</verbose>
        <overwrite>true</overwrite>
      </configuration>
    </plugin>
    ```

2. 在`resources`目录配置[generatorConfig.xml](https://gitee.com/qiudeqing/mall/blob/master/src/main/resources/generatorConfig.xml)
3. maven运行插件即可根据数据库表结构生成Java bean, 基本的dao和mapper


# 异常处理

1. 新建`src/main/resources/resources/error/404.html`,系统访问触发404错误会根据请求content-type返回对应内容.

# FAQs

## Cannot determine embedded database driver class for database type NONE

原因：没有配置数据库信息

```
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/imooc-demo?useUnicode=yes&characterEncoding=UTF-8&useSSL=false
    username: xxx
    password: xxx
```

## No Spring Session store is configured: set the 'spring.session.store-type' property




# 参考资料

- [Spring Boot 那些事][1]





[1]: https://www.bysocket.com/?page_id=1639
