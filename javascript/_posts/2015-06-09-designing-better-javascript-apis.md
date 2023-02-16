---
title: "[译]设计更好的JavaScript API"
---

原文链接: [http://www.smashingmagazine.com/2012/10/09/designing-javascript-apis-usability/][1]


本文列举了你在编写自己的工具库时需要考虑的最重要的事情. 我们重点关注如何使你的代码更容易被别人使用. 虽然这篇文章不是关于jQuery和如何编写jQuery插件的, 我们将以jQuery为例子讨论一些问题.

让我们设计开发者喜欢使用的API吧.

## 流式接口(Fluent Interface)

[流式接口][2]通常指方法链(但只说对了一半). 对于初学者来说这就像jQuery风格, 虽然我相信jQuery成功的因素之一是它的API风格, 但是这个风格不是它发明的. Marting Fowler在2005年提出[Fluent Interface][3]比jQuery发布早了一年.

## 方法链[Method Chaining]

[方法链][4]的主要目的是艰苦能使代码可读可理解. 使用方法链我们可以将代码组织成语句一样的序列, 使代码容易阅读, 理解.

    // regular API calls to change some colors and add an event listener
    var elem = document.getElementById('foobar');
    elem.style.background = 'red';
    elem.style.color = 'green';
    elem.addEventListener('click', function (e) {
      alert('hello world');
    }, false);

    // (imaginary) method chaining API
    DOMHelper.getElementById('foobar')
      .setStyle('background', 'red')
      .setStyle('color', 'green')
      .addEvent('click', function (e) {
        alert('hello world');
      });

## Command query separation

[Command and Query Separation][5]概念来自imperative programming. 修改对象状态的函数称为**命令**, 获取状态的函数称为**查询**. 原则上来说,查询返回数据, 命令修改状态, 不能用一个函数完成两个功能. 这个观念是大部分库使用getter和setter的原因. 然而前面提到的Fluent Interface返回对象子引用已经破坏了命令规则, 因为命令式不应该返回任何值的. jQuery中就有大量的例子与违背这条规则.

var $elem = jQuery('#foobar');

// CQS - command
$elem.setCss('background', 'green');

// CQS - query
$elem.getCss('color') === 'red';

// non-CQS -command
$elem.css('background', 'green');

// non-CQS - query
$elem.css('color') === 'red';

## Going Fluent

方法链已经完成fluent的大部分工作, 你还可以更进一步. 为了进一步展示fluent, 我们设计一个处理日期间隔的工具库. 时间间隔开始于一个日期并且结束于一个日期. 日期并不是必须要与时间间隔联系起来, 所以我们得到下面这个简单构造函数:

    // create new date interval
    var interval = new DateInterval(startDate, endDate);

    // get the calculated number of days the interval spans
    var days = interval.days();

粗看起来挺好的, 下面这个例子将展示一些问题:

    var startDate = new Date(2012, 0, 1);
    var endDate = new Date(2012, 11, 31);
    var interval = new DateInterval(startDate, endDate);
    var days = interval.days(); // 365

上面的例子中我们创建了一堆不是必须的变量. 为Date添加一个接受一个时间对象并返回时间间隔的方法可以很好地解决这个问题([修改原生对象的方法讨论][6]):

    // DateInterval creator for fluent invocation
    Date.prototype.until = function (end) {

      // if we weren't given a date, make one
      if (!(end instanceof Date)) {

        // create date frome given arguments,
        // proxy the constructor to allow for any parameters
        // the Date constructor would have taken natively
        end = Date.apply(null,
          Array.prototype.slice.call(arguments, 0)
        );
      }

      return new DateInterval(this, end);
    };

现在我们可以使用一种fluent, 简单的方法来创建`DateInterval`对象了.

    var startDate = new Date(2012, 0, 1);
    var interval = startDate.unitl(2012, 11, 31);
    var days = interval.days(); // 365

    // condensed fluent interface call:
    var days = (new Date(2012, 0, 1))
      .until(2012, 11, 31)  // returns DateInterval instance
      .days();  // 365

## Consistency

选择恰当的命名方式并且保持一致性.

## 处理参数

函数如何接受并处理参数是一个重要的课题. 需要考虑方法最可能被如何使用. 使用你的API的代码是否会反复调用一些函数? 为什么会出现这种反复调用? 你的API应该如何避免这种重复调用?

比如jQuery的`css()`方法用于设置DOM元素的样式:

    jQuery('#some-selector')
      .css('background', 'red')
      .css('color', 'white')
      .css('font-weight', 'bold')
      .css('padding', 10);

简化之后是这样调用的:

    jQuery('#some-selector')
      .css({
        'background': 'red',
        'color': 'white',
        'font-weight': 'bold',
        'padding': 10
      });


下面的函数对参数进行了详细的判断, 然后执行不同操作:

    DateInterval.prototype.values = function (name, value) {

      var map;
      var keys;

      if (jQuery.isPlainObject(name)) {
        // setting a map
        map = name;
      }

      // setting a value (on possibly multiple names), conver to map
      else if (value !== undefined) {

        keys = name.split(' ');
        map = {};
        for (var i = 0, len = keys.length; i < len; ++i) {
          map[keys[i]] = value;
        }
      }

      // getting all values
      else if (name === undefined) {
        return this.values;
      }

      // getting specific value
      else {
        return this.values[name];
      }

      for (var key in map) {
        this.values[key] = map[key];
      }

      return this;
    };

## 处理不同数据类型

当设计需要接受参数的函数时, 需要决定接受什么类型的参数. 计算两个时间之间相差天数的函数可能像下面这样:

    DateInterval.prototype.days = function (start, end) {
      return Math.floor((end - start) / 86400000);
    };

通过对参数进行检查和必要的转换,可以实现函数对不同类型参数的支持:

    DateInterval.prototype.days = function (start, end) {

      if (!(start instanceof Date)) {
        start = new Date(start);
      }

      if (!(end instanceof Date)) {
        end = new Date(end);
      }

      return Math.floor((end.getTime() - start.getTime()) / 86400000);
    };

添加上面的六行代码让函数具有处理Date对象, 数字时间戳, 以及`Sat Sep 08 2012 15:34:35 GMT+0200`这样的字符串形式.

上面的函数要求start在end之后, 我们可以进一步处理, 使顺序不同也能正常工作.

DateInterval.prototype.days = function (start end) {
  if (!(start instanceof Date)) {
    start = new Date(start);
  }

  if (!(end instanceof Date)) {
    end = new Date(end);
  }

  return Math.abs(Math.floor((end.getTime() - start.getTime()) / 86400000));
};

## 合理设置参数传递

参数个数太多, 顺序不方便记忆, 可以使用对象作为参数:

    function foo(opt) {
      opt = opt || {};

      console.log(opt.aaa);
      console.log(opt.bbb);
    }

    foo({
      aaa: '123',
      bbb: '444'
    });

## 默认参数

设置一个默认参数对象,然后根据用户传递参数进行覆盖

    function ajax(opt) {
      var o = jQuery.extend({}, ajax.defaults, opt || {});

      console.log(o);
    }

    ajax.defaults = {
      accepts: 'text',
      async: true,
      beforeSend: null,
      cache: false,
      complete: null
    };

    ajax({
      url: 'xxx',
      async: false
    });

## 可扩展性

### 回调(callback)

回调可以通过配置实现可扩展性. 当你觉得某些任务需要不同处理方式的时候, 回调可以让用户自定义处理方式.

    var default_options = {
      position: fucntion ($elem, $parent) {
        $elem.css($parent.position());
      }
    };

    function Widget(options) {
      this.options = jQuery.extend({}, default_options, options || {});
      this.create();
    }

    Widget.prototype.create = function () {
      this.$contaienr = $('<div></div>').appendTo(document.body);
      this.$thingie = $('<div></div>').appendTo(this.$container);
      return this;
    };

    Widget.prototype.show = function () {
      this.options.position(this.$thingie, this.$contaienr);
      this.$thingie.show();
      return this;
    };


    var widget = new Widget({
      position: function ($elem, $parent) {
        var position = $parent.position();

        position.left += $parent.width();
        position.top += $parent.height();
        $elem.css(position);
      }
    });

    widget.show();

### 事件

我们使用事件实现模块之间的通信, 特别是对于UI组件.

未完..

## 参考资料

- [Reusable Code][7]
- http://www.slideshare.net/slideshow/embed_code/5426258
- http://perfectionkills.com/extending-built-in-native-objects-evil-or-not/
- http://en.wikipedia.org/wiki/Command-query_separation
- http://en.wikipedia.org/wiki/Method_chaining
- http://martinfowler.com/bliki/FluentInterface.html
- http://en.wikipedia.org/wiki/Fluent_interface#JavaScript
- http://www.smashingmagazine.com/2012/10/09/designing-javascript-apis-usability/

[7]: http://www.slideshare.net/slideshow/embed_code/5426258
[6]: http://perfectionkills.com/extending-built-in-native-objects-evil-or-not/
[5]: http://en.wikipedia.org/wiki/Command-query_separation
[4]: http://en.wikipedia.org/wiki/Method_chaining
[3]: http://martinfowler.com/bliki/FluentInterface.html
[2]: http://en.wikipedia.org/wiki/Fluent_interface#JavaScript
[1]: http://www.smashingmagazine.com/2012/10/09/designing-javascript-apis-usability/
