---
title: servlet中的cookie操作
---

`HttpSession`使用COOKIE中的`JSESSIONID`值跟踪用户会话, 服务器通过HTTP头返回的cookie大致如下.其中可能包含注释, 域名, 失效时间, 路径等. 浏览器发送cookie到服务器时只包含键值对.

    Set-Cookie  Counter=7;
    Version=1;
    Comment="SetCookie Counter";
    Domain="localhost";
    Max-Age=86400;
    Expires=Thu, 15-Aug-2013 20:19:19 GMT;
    Path=/cookie/SetCookie

    Set-Cookie  Test="Test Cookie7";
    Version=1;
    Comment="Test Cookie"

servlet API使用`javax.servlet.http.Cookie`操作cookie, 这个类实现了`Serializable`和`Cloneable`接口.


`HttpServletRequest`类包含`getCookies()`获取请求中的Cookie数组.

`HttpServletResponse`包含`addCookie(Cookie c)`向响应头添加cookie.

`Cookie`构造函数的必须参数为名字和值, 其余都是可选参数.一些重要方法如下:

- `getComment()`: 获取cookie注释
- `getDomain()`: 获取cookie的domain, 与之对应的是`setDomain()`用于设置domain
- `getMaxAge()`: 获取秒为单位的cookie有效期, 与之对应的`setMaxAge()`设置有效期
- `getName()`: 获取cookie名, 没有对应设置函数, 只能通过构造函数设置cookie名
- `getPath()`: 获取cookie附属的path, 对应的`setPath()`可修改
- `getSecure()`: 返回true表示浏览器只通过安全协议发送cookie, `setSecure()`可修改
- `getValue()`: 获取cookie值, 对应`setValue()`
- `getVersion()`: 返回cookie遵循的协议版本, 有对应的设置函数
- `isHttpOnly()`: 检查cookie是否被标记只支持HTTP协议, 有对应设置函数
