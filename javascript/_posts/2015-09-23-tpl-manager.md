---
title: javascript中管理html代码段
---

在使用js模板引擎或者需要通过js向页面中插入一段html代码时, 模板文件或者html文件的获取通常可以采用ajax从服务器加载, 写在本页html文件的script标签中, 或者js字符串拼接起来.

- 有时候不可能从服务器获取
- 写纯js模块的时候不能使用script标签保存
- js字符串拼接麻烦,不好维护

这时候可以考虑使用函数注释处理的方法, 基本思路如下:

```
var tplFn = function () {
/*
<div>这里是模板</div>
*/
};

var tpl = tplFn.toString().split('\n').slice(2, -2).join('\n');
```
这样tpl就是需要保存的模板,函数需要保证大括号前后, 注释前后的换行.

不过这样也有一个缺点: **js压缩工具会压缩掉注释**

## 简单的模板管理工具

前面的方法每次要使用模板都要执行一系列的函数调用.很不方便,使用下面的模板管理工具可以更加方便:

```
var tpl = {
  story: function () {
/*
<div>这里是story模板</div>
*/
  },
  article: function () {
/*
<div>这里是article模板</div>
*/
  },
  get: function (name) {
    var me = this;
    var tplName = '_' + name;
    if (!me[tplName]) {
      var tplFn = me[name];
      if (typeof tplFn !== 'function') {
        throw new Error('找不到对应模板');
      }
      me[tplName] = tplFn.toString().split('\n').slice(2, -2).join('\n');
    }
    return me[tplName];
  }
};


tpl.get('story'); //  <div>这里是story模板</div>
```

这样在需要使用模板的地方直接调用`tpl.get(name)`即可获取, 多次调用还能更加方便
