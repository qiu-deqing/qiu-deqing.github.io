---
title: struts2 annotation
---

struts2支持通过命名约定和annotation实现action配置. [struts2 annotation helloworld][2]

# 配置

使用命名约定和annotation需要依赖`convention plugin`

```
<dependency>
  <groupId>org.apache.struts</groupId>
  <artifactId>struts2-convention-plugin</artifactId>
  <version>2.2.1</version>
</dependency>
```

# 扫描和转换

struts2通过扫描和转换实现action到java类的匹配

## 扫描顺序

1. 扫描包名包含`struts`, `struts2`, `action`, `actions`的包寻找注解类
2. 按照以下顺序确定注解类

    1. 实现`com.opensymphony.xwork2.Action`接口
    2. 继承`com.opensymphony.xwork2.ActionSupport`类
    3. 文件名以`Action`结束, 如`HelloAction`

## 类名转换

扫描到注解类之后, 执行名字转换实现与action的匹配, 转换顺序如下

1. 如果文件名包含`Action`, 去掉
2. 将文件名第一个字母转化为小写

即`LoginAction.java`最终映射为`login.action`

## 结果映射

默认执行action的`execute`之后到`WEB-INF/content`目录下寻找对应的视图.

视图名字为`<actionName>-<result>`如:`login-success`, `login-input`

后缀名可以是`jsp`, `vm`, `ftl`, 框架会按顺序查找

设置`struts.convention.result.path`可以自定义结果视图默认路径.


[2]: https://github.com/qiu-deqing/struts2-in-action/tree/master/helloword-annotated
[1]: http://struts.apache.org/docs/struts-2-annotations.html
