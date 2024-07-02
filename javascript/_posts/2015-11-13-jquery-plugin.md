---
title: jquery 插件
---

[jQuery官方插件教程][1]


## 提供默认参数给用户修改

方便用户自定义默认参数

```
$.fn.hilight = function (options) {
  var opts = $.extend({}, $.fn.hilight.defaults, options);
};

$.fn.hilight.defaults = {
  foreground: 'red',
  background: 'yellow'
};
```

## 提供功能函数给用户修改

方便用户自定义实现细节, 如元素隐藏效果,插件不可能全部枚举,推荐提供为用户自定义

```
$.fn.hilight = function (options) {

  return this.each(function () {
    var elem = $(this);
    var markup = elem.html();
    markup = $.fn.hilight.format(markup);
    elem.html(markup);
  });
};

$.fn.hilight.format = function (txt) {
  return '<strong>' + txt + '</strong>';
};
```

## 不确定是否公开的函数尽量不公开

## 为插件生成的元素添加一些标记

如果插件会创建元素, 应该给这些元素添加一些标记, 方便用户获取(如使用样式,获取元素).

```
var defaults = {
  wrapperAttrs: {
    class: 'gallery-wrapper'
  }
};

var settings = $.extend(true, {}, defaults, options);

var wrapper = $('<div />')
  .attr(settings.wrapperAttrs);
```

## 提供回调

插件有状态变化, 并且用户关心这些事件时, 可以通过回调完成

```
var defaults = {
  onImageShow: function () {}
};

nextButton.on('click', showNextImage);

function showNextImage() {
  var image = getNextImage();

  settings.onImageShow.call(image);
}
```

## Don't repeat yourself

- 如果在多个地方使用通过一个复杂的表达式, 将表达式的值赋给一个变量,在需要的地方使用该变量
- 如果在多个地方使用同一段代码, 将代码组织为函数, 相似功能的函数抽象为一个, 通过参数实现区分
- 不停检查代码将重复部分提取出来

## 熟练使用jQuery API

jQuery提供很多常用功能, 使用它们可以让代码更加可维护

## 避免过分优化

优化准则: **只优化必须优化的代码**, 先保证代码功能实现, 可维护, 可理解, 性能和文件大小确实影响很大时才优化重构.


## 绑定事件监听器时使用namespace

```
container.on('click.hilight', fn);
```

## 保存数据时不使用通用名字

```
// bad
$('#foo').data('text', 'hello world');

// good
$('#foo').data('yourPluginName', 'hello world');
```

## 支持data-*属性方式配置

除了调用函数时传入参数, 还可以支持通过data-*配置. [Bootstrap][5]同时支持js调用初始化和data-*属性自动初始化

```
<div data-option="{width:200, height: 100}"></div>
```


[6]: http://markdalgleish.com/2011/05/creating-highly-configurable-jquery-plugins/
[5]: http://getbootstrap.com/javascript/
[4]: http://www.smashingmagazine.com/2011/10/essential-jquery-plugin-patterns/
[3]: https://github.com/jquery-boilerplate/jquery-patterns
[2]: https://msdn.microsoft.com/en-us/magazine/ff696759
[1]: http://learn.jquery.com/plugins/
