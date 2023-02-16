---
title: struts2 interceptor
---

拦截器在action执行前后都会执行, 拦截器修改action状态, 取消action执行. 可以定义拦截器栈, 站内拦截器依次执行.

# Interceptor接口

```
public interface Interceptor extends Serializable {
    void destroy();
    void init();
    String intercept(ActionInvocation invocation) throws Exception;
}
```

拦截器初始化时调用`init()`执行初始化, 销毁时调用`destroy()`释放资源, 处理请求时调用`intercept()`.

所有action公用一个拦截器实例. 所以需要保证拦截器是线程安全的.

# 定义

通常继承`AbstractInterceptor`实现自定义拦截器

```
public class SimpleInterceptor extends AbstractInterceptor {
    public String intercept(ActionInvocation invocation) throws Exception {
        MyAction action = (MyAction)invocation.getAction();
        action.setDate(new Date());
        return invocation.invoke();
    }
}
```

# 配置

```
<package name="my-package" extends="struts-default">
    <interceptors>
        <interceptor name="test" class="SimpleInterceptor" />
    </interceptors>
</package>
```
