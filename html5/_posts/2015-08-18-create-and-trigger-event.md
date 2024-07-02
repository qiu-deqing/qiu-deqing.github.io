---
title: JavaScript创建并触发事件
---

只测试了Chrome和UC手机浏览器

## 创建自定义事件

使用[Event][1]构造函数可以直接创建时间,并在制定元素上触发:

```
var event = new Event('build.qiu');

elem.addEventListener('build.qiu', function (e) {}, false);

elem.dispatchEvent(event);
```

[在线demo][1]

## 创建自定义事件并携带数据

使用`CustomEvent`构造函数, 在配置参数的`detail`字段添加需要的参数.然后在`event.detail`中获取:

```
var event = new CustomEvent('build.qiu', {detail: 'hello'});

function eventHandle(e) {
  console.log(e.detail);
}
```

[在线demo][2]

## 触发build-in事件

```

<div id="d3">
  <style>
  #d3 .info,
  #d3 .trigger {
    min-height: 30px;
    background: #ddd;
  }
  </style>
  <p class="info"></p>
  <p class="trigger">click here to trigger event: build.qiu with detail data</p>
  <script>
  (function () {
    var info = document.querySelector('#d3 .info');
    var trigger = document.querySelector('#d3 .trigger');

    trigger.addEventListener('click', function (e) {
      var event = new CustomEvent('touchstart')
      info.dispatchEvent(event);
    }, false);

    info.addEventListener('touchstart', function (e) {
      info.textContent = 'received event: ' + e.type;

    }, false);
  }());
  </script>
</div>
```


[2]: http://qiudeqing.com/demo/html5/create-trigger-event.html#d2
[1]: http://qiudeqing.com/demo/html5/create-trigger-event.html#d1

