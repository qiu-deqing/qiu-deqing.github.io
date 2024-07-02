---
title: "[译]视图切换动画"
---

原文出处： [https://developers.google.com/web/fundamentals/look-and-feel/animations/animating-between-views?hl=en][1]


我们经常需要将用户切换到不同视图，切换中的动画可以让用户有更好的体验。

重点：

- 使用translation实现视图间移动，避免使用`left`,`top`这样的任何会触发布局的属性完成动画。
- 确保动画短且时髦（snappy）
- 考虑屏幕尺寸变大时的动画和布局，适合小屏幕的动画不一定适合大屏幕

动画的选择要改由视图切换类型来决定，例如模态对话框出现的动画和列表到详情的动画就有区别。

注意：

- 需要保证动画频率为60fps以避免卡顿
- 使用`will-change`属性让浏览器优化动画，视图动画中我们推荐`will-change: transform`

## 视图切换时使用translation

这里我们实现列表视图到详情视图的切换，用户点击列表项时详情滑动出现，替换掉列表页。

要实现这个效果你需要一个包含两个视图的容器，容器设置`overflwo:hidden`，这样就能保证两个视图并排也不出现水平滚动条。

![][i1]

容器对应的CSS如下：

    .container {
      width: 100%;
      height: 100%;
      overflow: hidden;
      position: relative;
    }

容器设置`position: relative`是为了视图可以方便地绝对定位，然后使用transform进行移动。使用transform性能比left属性好，因为left会触发布局和重绘。

视图样式如下：

    .view {
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;

      will-change: transform;
    }

为视图`transform`添加`transition`能够产生很好的滑动效果，我们使用`cubic-bezier`曲线来完成。

    .view {
      transition: transform 0.3s cubic-bezier(0.465, 0.183, 0.153, 0.946);
    }

需要隐藏的视图应该translate到右边：

    .details-view {
      transform: translateX(100%);
    }

下面添加一些JavaScript实现类的切换。

    var container = document.querySelector('.container');
    var backButton = document.querySelector('.back-button');
    var listItems = document.querySelectorAll('.list-item');

    /**
     * Toggles the class on the container so that
     * we choose the correct view.
     **/
    function onViewChange(evt) {
      container.classList.toggle('view-change');
    }

    /**
     * when you click on a list item bring on the details view
     **/
    for (var i = 0, len = listItems.length; i < len; ++i) {
      listItems[i].addEventListener('click', onViewChange, false);
    }

    backButton.addEventListener('click', onViewChange, false);

最后添加必要的CSS

    .view-change .list-view {
      transform: translateX(-100%);
    }

    .view-change .details-view {
      transform: translateX(0);
    }

你可以扩展这个例子到多视图模式，基本理念依然不变：不可见的视图需要隐藏在屏幕外面，然后在需要的时候显示出来，并且把当前视图移除。

这里是完整的[在线demo][2]

<iframe width="100%" height="300" src="//jsfiddle.net/gwcnx505/embedded/result,css,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

注意：

跨浏览器实现这个效果是很有挑战性的，比如在iOS中需要添加`-webkit-overflwo-scrolling: touch`来‘reenable’ fling scrolling，但是你不需要控制具体的axis。

以上的方法可以使用到其他滑动元素，例如侧边导航。唯一的区别在于你不需要移动其他视图。





[1]: https://developers.google.com/web/fundamentals/look-and-feel/animations/animating-between-views?hl=en
[2]: http://qiudeqing.com/demo/animate/animate-between-view.html

[i1]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150613-3@2x.png
