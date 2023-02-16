---
title: "[译]CSS VS JavaScript动画"
---

原文链接:[https://developers.google.com/web/fundamentals/look-and-feel/animations/css-vs-javascript?hl=en][1]

## 简介

通常有CSS和JavaScript两种方法来实现动画. 选择那种方案实现由项目和所需要实现的效果决定.

简而言之:

- 简单变化使用CSS动画, 如UI元素状态切换
- 需要高级效果时使用JavaScript动画, 如: 弹跳, 暂停, 倒带
- 如果使用JavaScript实现, 可以采用[TweenMax][2]或者轻量级实现[TweenLite][3]

大部分基础动画都可以用CSS或者JavaScript实现, 但是需要的时间和精力不同.[CSS VS JavaScript Performance][4]也不同. 以下是一些参考法则:

- **UI元素状态转移的小动画使用CSS**. CSS transition和animation在显示菜单项, tooltip方面是最佳选择. 可以使用JavaScript来控制状态, CSS完成动画.
- **对动画控制力要求高的时候使用JavaScript**. 需要停止, 暂停, 变速, 倒退操作的动画需要使用JavaScript实现.


## Animate with CSS

最方便实现动画的方法是CSS.

下面的CSS会把元素向X和Y方向移动100px.

    .box {
      transform: translate(0, 0);
      transition: transform 500ms;
    }

    .box.move {
      transform: translate(100px, 100px);
    }

demo如下:

<iframe width="100%" height="300" src="//jsfiddle.net/d5y6buah/embedded/result,css,js,html/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

前面的例子中使用JavaScript控制状态, 为元素设置类, 由CSS来完成动画. 如果需要监测动画完成.可以注册对应监听器:

    var box = document.querySelector('.box');
    box.addEventListener('transitionend', onTransitionEnd, false);

    function onTransitionEnd() {}

除了transition之外还可以使用animation实现动画, 这样可以提供更强的控制能力.

可以使用animation实现一个多属性变化的无线重复动画:

    html {
      background: #f2f2f2;
    }

    .box {
      animation-name: movingBox;
      animation-duration: 1300ms;
      animation-iteration-count: infinite;
      animation-direction: alternate;

      width: 100px;
      height: 100px;
      background: #fff;
      box-shadow: 0 10xp 20px rgba(0,0,0,0.5);
    }

    @keyframes movingBox {
      0% {
        transform: translate(0, 0);
        opacity: 0.3;
      }

      25% {
        opacity: 0.9;
      }

      50% {
        transform: translate(100px, 100px);
        opacity: 0.2;
      }

      100% {
        transform: translate(30px, 30px);
        opacity: 0.8;
      }
    }

demo如下

<iframe width="100%" height="300" src="//jsfiddle.net/vj7cpo7L/embedded/result,css,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

## JavaScript实现动画

JavaScript实现动画比CSS复杂, 但是为开发人员提供了更强的控制能力, 通常的方式是使用`requestAnimationFrame`, 然后在每一帧上设置需要的属性.

注意:

你可能在其他地方看见使用`setInterval`或者`setTimeout`实现动画. 这样的动画不能与屏幕刷新频率同步, 很容易出现跳帧和卡顿. 应该始终避免使用这种方法, 始终使用requestAnimationFrame.

下面的JavaScript代码实现前面的动画

    function Box() {

      var animationStartTime = 0;
      var animationDuration = 500;
      var target = document.querySelector('.box');

      this.startAnimation = function () {
        animationStartTime = Date.now();
        requestAnimationFrame(update);
      };

      function update() {
        var currentTime = Date.now();
        var positionInAnimation = (currentTime - animationStartTime) / animationDuration;

        var xPosition = positionInAnimation * 100;
        var yPosition = positionInAnimation * 100;

        target.style.transform = 'translate(' + xPosition + 'px,' + yPosition + 'px)';

        if (positionInAnimation <= 1) {
          requestAnimationFrame(update);
        }
      }
    }

    var box = new Box();
    box.startAnimation();

demo如下:

<iframe width="100%" height="300" src="//jsfiddle.net/ynwt02bf/embedded/result,js,css,html/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>


随着功能的增加上面的代码会变得复杂并且难以管理，通常来说选择一个成熟的js动画库能带来很多好处。例如jQuery的`animate()`函数，或者[TweenMax][5]这样的专门动画库。

使用JavaScript动画的好处是你可以完全控制元素的样式，并且可以随时暂停，停止，倒退。

## 参考资料

- [https://github.com/greensock/GreenSock-JS/tree/master/src/minified][5]
- [https://developers.google.com/web/fundamentals/look-and-feel/animations/animations-and-performance.html#css-vs-javascript-performance][4]
- [http://greensock.com/tweenlite][3]
- [http://greensock.com/tweenmax][2]
- [https://developers.google.com/web/fundamentals/look-and-feel/animations/css-vs-javascript?hl=en][1]

[5]: https://github.com/greensock/GreenSock-JS/tree/master/src/minified
[4]: https://developers.google.com/web/fundamentals/look-and-feel/animations/animations-and-performance.html#css-vs-javascript-performance
[3]: http://greensock.com/tweenlite
[2]: http://greensock.com/tweenmax
[1]: https://developers.google.com/web/fundamentals/look-and-feel/animations/css-vs-javascript?hl=en
