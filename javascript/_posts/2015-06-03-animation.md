---
title: "[译]javascript动画"
category: animation
---

# javascript动画

原文出处[http://javascript.info/tutorial/animation][1]

javascript动画基本思想是按照一定间隔修改DOM元素的样式或者canvas内容.通常我们使用框架来处理动画,学习动画的实现能够提高技能并创造复杂的动画.

以下是一个常用的模式:

    var id = setInterval(function () {
      // show the current frame
      if (/* finished */) {
        clearInterval(id);
      }
    }, 10);

在这里我们使用10ms的间隔,大部分框架默认使用10-15ms作为间隔,间隔越少动画越平滑,如果动画需要很多计算,可能消耗过多CPU资源,此时需要增加间隔.

在这里我们使用`setInterval`而不是`setTimeout`是因为 we want a frame once per interval, not a fixed delay between frames.查看[理解计时器:setTimeout和setInterval][2]了解更多内容.(注:最好使用requestAnimationFrame)

下面是一个简单的例子,每10ms增加元素left值1px.直到100;

<iframe width="100%" height="300" src="//jsfiddle.net/7qub6p9n/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

    <style>
    .example_path{
      position:relative;
      overflow:hidden;
      width:530px;
      height:30px;
      border:3px solid #000;
      }
    .example_path .example_block {
      position:absolute;
      background-color:blue;
      width:30px;
      height:20px;
      padding-top:10px;
      text-align:center;
      color:#fff;
      font-size:10px;
      white-space:nowrap;
    }
    </style>

    <div id="d1">
      <h2>点击下面的元素开始动画</h2>
      <div class="example_path">
        <div class="example_block"></div>
      </div>
      <script>
      !function () {
        var p = document.querySelector('#d1 .example_path');
        var c = document.querySelector('#d1 .example_block');
        console.log(p, c)
        p.addEventListener('click', function () {
          move(c);
        }, false);

        function move(elem) {
          var left = 0;

          function frame() {
            ++left; // update parameters
            elem.style.left = left + 'px';  // show frame
            if (left === 100) {
              clearInterval(id);
            }
          }
            var id = setInterval(frame, 10);

        }
      }();
      </script>
    </div>

## animation重构

前面是一个最基本的动画,我们引入一下参数来实现一个更通用的动画功能.

- **delay**:  两个动画帧之间的间隔,单位为ms,例如10ms
- **duration**: 完整动画所需时间,单位为ms,例如1000ms

在动画开始时我们需要

- **start**:  记录动画开始的时间,通常`start = new Date()`

作为动画的核心,我们为每一帧计算:

- **timePassed**: 从动画开始到现在所经过的时间,取值通常是0到duration之间,由于浏览器计时器的不精确,这个值可能大于duration
- **progress**:   动画已经过的百分数,对每一帧来说`progress = timePassed / duration`,取值通常从0到1.
- **delta(progress)**:  一个返回当前动画进度的函数,例如,我们需要将`height`从0%变化到100%.可以使用线性方法来完成如下:![][4]

    或者我们想要实现非线性的方法如下:![][5]

    `delta(progress)`是一个从时间进度映射到动画进度的函数.动画进度也是一个从0到1得数字.

- **step(delta)**:  实际操作动画效果的函数,使用动画进度`delta`作为输入并且设置对应效果,例如设置高度的例子中,应该是这样一个函数:

      function step(delta) {
        elem.style.height = 100 * delta + '%';
      }

将前面的总结起来,主要参数如下:

- `delay`作为`setInterval`的第二个参数
- `duration`控制动画的时长
- `progress`记录动画已经经过的时间比例,值从0到1
- `delta`根据当前时间进度计算动画进度的函数
- `step`根据当前动画进度执行动画实际操作的函数

## 通用动画

根据前面的讨论得到一个灵活的动画核心.下面的`animate`函数执行时间管理,将实际工作交给`delta`和`step`

    /**
     * 通用动画函数
     * @param opts {Object} 动画所需参数
     * @param opts.delay {number} 动画两帧之间的间隔,单位为ms
     * @param opts.duration {number} 动画总时长,单位为ms
     * @param opts.delta {function} 时间进度到动画进度的映射函数,
     *  参数为[0,1]之间的时间进度,返回值为[0,1]之间的动画进度
     * @param opts.step {function} 实际执行动画帧状态修改的函数,
     *  参数为动画进度delta,然后执行所需的操作
     **/
    function animate(opts) {

      var start = new Date();

      var id = setInterval(function () {

        var timePassed = new Date() - start;
        var progress = timePassed / opts.duration;

        if (progress > 1) {
          progress = 1;
        }

        var delta = opts.delta(progress);
        opts.step(delta);

        if (progress === 1) {
          clearInterval(id);
        }
      }, opts.delay || 10);
    }

### 实际使用

在前面通用动画的基础上重写前面的移动动画如下:

    /**
     * 移动一个元素的动画
     * @param element {HTMLElement} 需要动画移动的元素
     * @param delta {function} 时间进度到动画进度的映射函数
     * @param duration {number} 动画总时长
     **/
    function move(element, delta, duration) {

      var to = 500;

      animate({
        delay: 10,
        duration: duration || 1000,
        delta: delta,
        step: function (delta) {
          element.style.left = to * delta + 'px';
        }
      });
    }

以上的函数根据用户参数将工作交给`animate`进行控制,其中以下一些需要注意

- `delta = function (p) { return p; }`:我们在调用时使用最简单的线性调度
- `step`: 执行简单的映射

实际使用如下:

    move(elem, function (p) {
      return p;
    });

<iframe width="100%" height="300" src="//jsfiddle.net/0c116ypo/embedded/result,js,css,html/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>


## Maths, the function of progress delta

动画的本质是遵循指定的规则随着时间修改属性.在javascript动画中,规则通过`delta`函数实现.

不同的delta函数产生不同动画速度,加速度以及其他行为.

本节介绍几个常用函数

### 线性delta

    function linear(progress) {
      return progress;
    }

前面的例子使用的就是线性delta.

### n平方

常见的非线性delta是平方函数和立方函数[在线demo][9]

    function quad(progress) {
      return Math.pow(progress, 2);
    }

平方函数变化曲线:

![][7]

### 圆弧:变化弧线为圆的一段弧

[在线demo][10]函数:

    function circ(progress) {
      return 1 - Math.sin(Math.acos(progress));
    }

变化曲线:

![][8]


### 弓函数

功函数产生的动画就像射箭一样:先向后拉,然后向前射出.[在线demo][12]

功函数需要另外一个参数x, 即coefficient of elasticity,用于定义向后拉得距离.

    function bow(progress, x) {
      return Math.pow(progress, 2) * ((x + 1) * progress - x);
    }

下面是x = 1.5时的函数曲线

![][11]

## 修改step

除了前面例子提到的属性,可以修改opacity, width, height等等各种属性.

### 颜色高亮

下面的highlight函数用于颜色改变动画

    function highlight(elem) {
      var from = [255, 0, 0],
        to = [255, 255, 255];

      animate({
        delay: 10,
        duration: 1000,
        delta: linear,
        step: function (delta) {
          elem.style.backgroundColor = 'rgb(' +
            Math.max(Math.min(parseInt((delta * (to[0]-from[0])) + from[0], 10), 255), 0) + ',' +
            Math.max(Math.min(parseInt((delta * (to[1]-from[1])) + from[1], 10), 255), 0) + ',' +
            Math.max(Math.min(parseInt((delta * (to[2]-from[2])) + from[2], 10), 255), 0) + ')';
        }
      });
    }

## 参考资料

- [http://javascript.info/tutorial/animation][1]




[1]: http://javascript.info/tutorial/animation
[2]: http://javascript.info/tutorial/settimeout-setinterval
[4]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150603-1@2x.png
[5]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150603-2@2x.png
[6]: http://qiudeqing.com/demo/javascript/animation-move.html#d2
[7]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150604-1@2x.png
[8]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150604-2@2x.png
[9]: http://qiudeqing.com/demo/javascript/animation-move.html#d3
[10]: http://qiudeqing.com/demo/javascript/animation-move.html#d4
[11]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150604-4@2x.png
[12]: http://qiudeqing.com/demo/javascript/animation-move.html#d5
