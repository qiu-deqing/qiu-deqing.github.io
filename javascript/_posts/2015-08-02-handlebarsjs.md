---
title: handlebars基础使用及常用扩展
---


## 基础使用

[Handlebars官网][1]有详细介绍, 核心部分提供了最常用的变量替换,循环,选择功能.
同时提供了良好的扩展功能,通过自定义helper完成所需任务.


## 常用扩展

Handlebars只提供了最核心的功能,很多功能需要自己添加,以下是个人[常用扩展][2]

```
/**
 * 常用的handlebars扩展helper
 *
 * 1. eq
 * 用法: {{#eq left right}} block {{else}} inverse {{/eq}} else为可选
 * 含义: 如果left严格等于right, 渲染block, 否则渲染inverse
 *
 * 2. gt
 * 用法: {{#gt left right}} block {{else}} inverse {{/gt}} else为可选
 * 含义: 如果left大于right, 渲染block, 否则渲染inverse
 *
 * 3. gte
 * 用法: {{#gte left right}} block {{else}} inverse {{/gte}} else为可选
 * 含义: 如果left大于等于right, 渲染block, 否则渲染inverse
 *
 * 4. lt
 * 用法: {{#lt left right}} block {{else}} inverse {{/lt}} else为可选
 * 含义: 如果left小于right, 渲染block, 否则渲染inverse
 *
 * 5. lte
 * 用法: {{#lte left right}} block {{else}} inverse {{/lte}} else为可选
 * 含义: 如果left小于等于right, 渲染block, 否则渲染inverse
 *
 * 6. even
 * 用法: {{#even num}} block {{else}} inverse {{/even}} else为可选
 * 含义: 如果num为偶数, 渲染block, 否则渲染inverse
 *
 * 7. odd
 * 用法: {{#odd num}} block {{else}} inverse {{/odd}} else为可选
 * 含义: 如果num为奇数, 渲染block, 否则渲染inverse
 *
 * 8. multiple
 * 用法: {{#multiple num base}} block {{else}} inverse {{/multiple}} else为可选
 * 含义: 如果num为base的倍数, 渲染block, 否则渲染inverse
 *
 **/

// 比较两个变量是否相等
Handlebars.registerHelper('eq', function (left, right, options) {
  if (arguments.length !== 3) {
    throw new Error('helper "eq" needs 2 arguments');
  }
  if (left === right) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});

// 比较第一个变量是否大于第二个
Handlebars.registerHelper('gt', function (left, right, options) {
  if (arguments.length !== 3) {
    throw new Error('helper "gt" needs 2 arguments');
  }
  if (left > right) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});

// 比较第一个变量是否大于等于第二个
Handlebars.registerHelper('gte', function (left, right, options) {
  if (arguments.length !== 3) {
    throw new Error('helper "gte" needs 2 arguments');
  }
  if (left >= right) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});


// 比较第一个变量是否小于第二个
Handlebars.registerHelper('lt', function (left, right, options) {
  if (arguments.length !== 3) {
    throw new Error('helper "lt" needs 2 arguments');
  }
  if (left < right) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});

// 比较第一个变量是否小于等于第二个
Handlebars.registerHelper('lte', function (left, right, options) {
  if (arguments.length !== 3) {
    throw new Error('helper "lte" needs 2 arguments');
  }
  if (left <= right) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});

// 判断一个整数是否为奇数
Handlebars.registerHelper('even', function (num, options) {
  if (arguments.length !== 2 ) {
    throw new Error('helper "even" needs 1 argumentx');
  }
  if (num % 2 === 0) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});


// 判断一个整数是否为偶数
Handlebars.registerHelper('odd', function (num, options) {
  if (arguments.length !== 2 ) {
    throw new Error('helper "odd" needs 1 argumentx');
  }
  if (num % 2 === 1) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});


/**
 * 判断一个整数是否为制定数字的整数倍
 * @param num {number} 需要判断的数字
 * @param base {number} 被比较的因子
 **/
Handlebars.registerHelper('multiple', function (num, base, options) {
  if (arguments.length !== 3 ) {
    throw new Error('helper "multiple" needs 1 argumentx');
  }
  if (num % base === 0) {
    return options.fn(this);
  } else {
    return options.inverse(this);
  }
});

```







[2]: https://github.com/qiu-deqing/snippet/blob/master/handlebars-helpers.js
[1]: http://handlebarsjs.com/
