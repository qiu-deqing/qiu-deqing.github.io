---
title: 触摸元素状态
---

DOM元素有四种状态: 默认, focus, hover, active. CSS通过伪类对这些状态应用样式:

```

.btn {
    display: block;
    height: 44px;
    line-height: 44px;
    text-align: center;
    border-radius: 4px;
    background-color: #000;
    margin-bottom: 20px;
}

.btn-focus,
.btn:focus {
  background-color: #333;

  /* The outline parameter surpresses the border
  color / outline when focused */
  outline: 0;
}
.btn-hover,
.btn:hover {
  background-color: #666;
}


.btn-active,
.btn:active {
  background-color: #999;
}
```

# hover和focus的粘滞性

大部分无线浏览器上敲击元素会触发hover或focus或者两者.当敲击结束时元素可能会保留hover或者focus的样式.

`<a>`元素和`<button>`元素在不同浏览器下有不同的表现, 有的保留hover样式有的保留focus样式.

# iOS上实现active状态

在iOS上鼠标事件发送非常快导致[the down or active state is never received][3]. 因此`:active`状态只有元素设置了touch事件监听器时才会触发. 比如我们可以通过监听`touchstart`事件让元素触发active状态.

应该检测用户代理以确保只在iOS设备上添加监听器

给body添加监听器可以让所有元素都受益, 但是当页面滚动时会影响性能.

```
document.addEventListener('DOMContentLoaded', function () {
    if (/iP(hone|ad)/.test(window.navigator.userAgent)) {
        document.body.addEventListener('touchstart', function () {}, false)
    }
})
```

也可以只给可交互元素绑定事件, 这样能减轻性能问题, 不过后续新添加的元素需要单独添加监听器

```
document.addEventListener('DOMContentLoaded', function () {
    if (/iP(hone|ad)/.test(window.navigator.userAgent)) {
        var elements = document.querySelectorAll('.btn')
        var noop = function () {}
        for (var i = 0; i < elements.length; ++i) {
            elements[i].addEventListener('touchstart', noop, false)
        }
    }
})
```

# 覆盖链接敲击高亮样式
    Safari和Chrome都给链接敲击设置了颜色高亮, 敲击链接时会显示一个高亮颜色, 然后高亮消失. 如果为链接的`:hover`状态设置了不同背景颜色, 就会先看到高亮颜色, 高亮消失之后才看到设置的背景颜色. 通过[-webkit-tap-hilight-color][1]属性可以进行覆盖:
    ```
    a {
        -webkit-tap-highlight-color: transparent;
    }
    ```

    在[Windows Phone的IE上同样有默认高亮样式][2]. 禁止的方法是通过meta标签
    ```
    <meta name="msapplication-tap-highlight" content="no">
    ```

# 禁止用户选择文本

当用户长按屏幕时一些浏览器启动文本选择功能. 通过`user-select`可以禁止用户选择文本. **如果元素包含了电话号码, 电子邮件等用户很可能需要复制的信息时, 不应该禁止用户选择文本**

```
-webkit-user-select: none;
   -moz-user-select: none;
    -ms-user-select: none;
        user-select: none;
```

在PC上经常使用`:hover`为用户鼠标滑过提供反馈, 无线浏览器没有hover
状态, 触目元素时需要提供反馈可以使用`:active`状态.

所有无线浏览器都支持`<a>`元素的active状态, 其他元素有兼容性问题.

```
a:active {
    background-color: #333;
}
```

iOS的Safari在非`<a>`标签还需要给元素或文档添加`touchstart`监听器

```
document.addEventListener('touchstart', function () {}, true)
```


iOS的Safari还需要设置特有的css:

```
-webkit-tap-hilight-color: rgba(0, 0, 0, 0);
```


# 参考资料

- [https://developers.google.com/web/fundamentals/design-and-ui/input/touch/active-states?hl=en]()
- [https://developer.apple.com/library/mac/documentation/AppleApplications/Reference/SafariWebContent/AdjustingtheTextSize/AdjustingtheTextSize.html#//apple_ref/doc/uid/TP40006510-SW5]()


[3]: https://developer.apple.com/library/mac/documentation/AppleApplications/Reference/SafariWebContent/AdjustingtheTextSize/AdjustingtheTextSize.html#//apple_ref/doc/uid/TP40006510-SW5
[2]: https://msdn.microsoft.com/library/bg182645(v=vs.85).aspx#tapHighlight
[1]: https://developer.mozilla.org/en/docs/Web/CSS/-webkit-tap-highlight-color
