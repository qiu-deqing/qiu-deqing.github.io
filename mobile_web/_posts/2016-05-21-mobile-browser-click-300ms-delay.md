---
title: 无线端浏览器click事件300ms延迟
---


# 起因

2007年苹果为了解决iPhone Safari访问PC页面的缩放问题, 提供了一个**双击缩放**功能.当用户第一次触摸屏幕的时候浏览器等待300ms才能判断用户是要点击(click)还是缩放(zoom),这就造成用户触摸屏幕到click事件触发存在300ms延迟.

触摸屏幕事件流程如下:

1. touchstart
2. touchend
3. Wait 300ms in case of another tap
4. click

受到这个延迟影响的场景有:

- JavaScript监听的click事件
- 基于click的交互的元素, 例如链接, 表单元素

随着iPhone的成功, 后续的无线浏览器复制了它的大部分操作习惯, 其中就包括双击缩放, 这也成为主流浏览器的一个功能.

这300ms延迟在平时浏览网页不会带来严重问题, 但是对于高性能web app就是一个严重的问题, 响应式设计的流行也让双击缩放逐渐失去用武之地.

[点击本页面测试click延迟](http://output.jsbin.com/ixibol/6)

# 浏览器解决方案


- 取消禁止缩放页面的延迟
    双击是为了缩放页面, 在不能缩放的页面禁止延迟也就理所当然, 安卓版的Chrome, Opera, BlackBerry和Firefox在设置以下meta标签的页面中取消了延迟, [webkit在2015-10-14也实现了支持](https://bugs.webkit.org/show_bug.cgi?id=149968)


    ```
    <meta name="viewport" content="user-scalable=no">
    或者
    <meta name="viewport" content="maximum-scale=1.0, minimum-scale=1.0">
    ```

- 取消为无线设计页面的延迟
    考虑到禁止缩放会带来可用性和可访问性的问题, Chrome 32开始[取消viewport设置为width小于等于device-width页面两次敲击缩放功能](https://codereview.chromium.org/18850005/), Opera, UC9也都实现了这一功能, Firefox还没有实现.[webkit在2015-10-27实现了支持](http://trac.webkit.org/changeset/191644)

    ```
    <meta name="viewport" content="width=device-width">
    ```

- 指针事件(Pointer Event)
    指针事件使用一套事件模型统一鼠标, 触屏, 笔触等所有输入事件, 其中引入的CSS属性`touch-action`将如何响应用户操作的决定权交给页面设计者, 具体兼容性可以查询[caniuse touch-action](http://caniuse.com/#search=touch-action), [webkit在2015-10-22实现了支持](http://trac.webkit.org/changeset/191452)

    ```
    html {
        -ms-touch-action: manipulation;
            touch-action: manipulation;
    }
    ```

# 页面实现者解决方案

从上面可以看出再过两年300ms延迟将成为历史, 在浏览器解决这个问题之前还是需要页面实现者提供兼容方案

- 监听touchend事件
    `touchend`不会有延迟, 感觉替代`click`完全可以, 但是存在一些问题:

    - 响应式页面在PC上需要依赖`click`事件, 同时监听两种事件会导致任务执行两次.

    在页面功能简单的时候可以采用这个方案, 如果复杂了就不好管理控制.

- 指针事件polyfill
    指针事件polyfill有以下代表:

    - [jquery PEP](https://github.com/jquery/PEP)
    - 微软[HandJS](http://handjs.codeplex.com/)
    - [@Rich-Harris](https://github.com/Rich-Harris)的[Points](https://github.com/Rich-Harris/Points)

    这些polyfill最难的地方在于在飞IE浏览器中模拟`touch-action`, 由于浏览器会忽略不支持的CSS属性, 唯一能够检测开发者是否声明了`touch-action: none`的办法就是JavaScript请求并解析所有样式表. HandJS就是这样做的, 但是实现起来在性能上很难做好.

    PEP通过判断标签上的`touch-action`属性, 相对来说会简单一些

    对于开发者来说如果对指针事件感兴趣, 上面的polyfill就非常合适, 如果只是想寻找解决300ms延迟的方法, 上面的方案就over skill了. 因为他们都是资源密集型的方案. 接下来我们看看专门为了解决300ms而生的解决方案.

- FastClick
    [FastClick](https://github.com/ftlabs/fastclick)是专门为解决移动端浏览器300ms延迟而开发的轻量级库. 它检测到`touchend`时, 立即触发`click`时间, 并把浏览器之后触发的`click`事件阻止掉, 在不存在300ms的浏览器上它什么也不做. 目前来说FastClick是最简单实用的方案.


# 总结

结合前面的讨论个人采用以下方案

- 设置viewport

    ```
    <meta name="viewport" content="width=device-width, initial-scale=1">
    ```

- 设置`touch-action`

    ```
    html {
        -ms-touch-action: manipulation;
            touch-action: manipulation;
    }
    ```

- 引入FastClick

    ```
    if ('addEventListener' in document) {
        document.addEventListener('DOMContentLoaded', function() {
            FastClick.attach(document.body);
        }, false);
    }
    ```

# 2016-11-14更新


苹果在2016-3-21发布了iOS9.3, 其中正式[移除了300ms延迟][1], 加上[chrome等浏览器消除300ms延迟][2], 只需要设置`viewport`和`touch-action`就可以直接用click事件了



# 参考资料


- [https://webkit.org/blog/5610/more-responsive-tapping-on-ios/]()
- [http://www.telerik.com/blogs/what-exactly-is.....-the-300ms-click-delay]()
- [https://developers.google.com/web/updates/2013/12/300ms-tap-delay-gone-away][2]
- [http://www.quirksmode.org/blog/archives/2014/04/suppressing_the.html]()
- https://adactio.com/journal/10019



[2]: https://developers.google.com/web/updates/2013/12/300ms-tap-delay-gone-away
[1]: http://www.mobilexweb.com/blog/safari-on-ios-9-3-picture-shrink-fit-iphone-se
