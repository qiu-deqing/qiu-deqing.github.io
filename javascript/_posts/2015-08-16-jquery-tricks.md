---
title: jQuery技巧

---

主要摘自[tutorialzine: jQuery tickshots][1]


## DOM Manipulation

### Execute Page Specific Code

如果多个页面包含同一个js文件, 不同页面需要执行其中的一部分代码. 提供一个页面路由函数进行管理.

```
var route = {
  _routes: {},

  add: function (url, action) {
    this._routes[url] = action;
  },

  run: function () {
    for (var pattern in this._routes) {
      if (location.href.match(pattern)) {
        this._routes[pattern];
      }
    }
  }
};

// use
route.add('002.html', function () {
  alert('002');
});

route.add('products.html', function () {
  alert('products');
});
```

### 阻止右键点击弹出菜单

```
$(document).on('contextmenu', function (e) {
  e.preventDefault();
})
```

### 阻止其他页面使用iframe嵌入本页

```
if (window != window.top) {
  window.top.location = window.location;
}
```

### 使用`<a>`解析url

浏览器环境下使用`<a>`解析url比正则表达式方便

```
var url = 'http://tutorialzine.com/books/jquery-trickshots?trick=12#comments';
var a = document.createElement('a');
a.href = url;

console.log(a.hostname);
console.log(a.search);
```

### 禁止文本被选中

```
$('p.desc').attr('unselectable', 'on')
          .css('user-select', 'none')
          .on('selectstart', false);
```

## Performance


### 使用原生JavaScript

新建jQuery对象需要消耗额外资源, 原生JavaScript能实现的功能不需要新建jQuery对象

```
$('#colors li').each(function () {
  console.log(this.id);
});
```

[1]: http://tutorialzine.com/books/jquery-trickshots/
