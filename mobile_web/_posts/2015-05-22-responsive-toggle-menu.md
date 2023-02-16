---

title: 响应式切换菜单

---

# 响应式可切换菜单

本文展示一个PC First的响应式菜单,宽屏幕下显示普通菜单,窄屏幕上收缩菜单,显示菜单按钮,点击弹出.[在线demo][2]

基本思路如下:

1. 确定基本HTML,编写宽屏幕下普通菜单样式
2. 设置media query,在窄屏幕下隐藏菜单,显示菜单切换图标
3. 为菜单切换图标设置监听器,切换菜单状态

## 基本HTML结构和普通样式

HTML中需添加一个切换图标元素,这里使用的是[iconfont][1],默认情况下隐藏.

    <nav class="page-nav">

      <span class="menu-toggle js-menu-toggle iconfont">&#xe600;</span>

      <ul class="js-menu menu">
        <li class="menu-item"><a href="#">AAA</a></li>
        <li class="menu-item"><a href="#">bbb</a></li>
        <li class="menu-item"><a href="#">CCC</a></li>
        <li class="menu-item"><a href="#">ddd</a></li>
      </ul>
    </nav>

    <p>Content</p>

以下是对应CSS,两部分结合得到标准的水平菜单导航结构


    <style>
    @font-face {
      font-family: 'iconfont';
      src: url('//at.alicdn.com/t/font_1432227064_002895.eot'); /* IE9*/
      src: url('//at.alicdn.com/t/font_1432227064_002895.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
      url('//at.alicdn.com/t/font_1432227064_002895.woff') format('woff'), /* chrome、firefox */
      url('//at.alicdn.com/t/font_1432227064_002895.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
      url('//at.alicdn.com/t/font_1432227064_002895.svg#iconfont') format('svg'); /* iOS 4.1- */
    }

    /**
     *@ name:        iconfont.css V1.1.0
     *@ author:      一丝
     *@ update:      2013-12-24 15:28:59
     * 1. 防止读屏器读出无意义的图标
     * 2. 防止 OS X 中图标视觉变粗和细节丢失的问题
     * 3. Fiefox 25 开始支持「-moz-osx-font-smoothing:auto(默认)|grayscale」
     * Demo: http://jsbin.com/iWItiQe/2
     */
    .iconfont{
        font-family: "iconfont";
        font-size: 16px;
        font-style: normal;
        font-weight: normal;
        font-variant: normal;
        display: inline-block;
        /* 1 */
        speak: none;
        /* 2 */
        -webkit-font-smoothing: antialiased;
        /* 3 */
        -moz-osx-font-smoothing: grayscale;
    }


    .menu-toggle {
      display: none;
      cursor: pointer;
      background: #ccc;
      margin-bottom: 5px;
    }
    .menu {
      list-style: none;
      margin: 0;
      padding: 0;
      overflow: hidden;
    }
    .menu-item {
      float: left;
    }
    .menu-item a {
      display: block;
      height: 30px;
      line-height: 30px;
      padding: 0 20px;
      color: #333;
      text-decoration: none;
      background: #eee;
    }
    .menu-item a:hover {
      background: #ccc;
    }
    </style>

## 设置media query,在窄屏幕下隐藏菜单,显示菜单切换图标

在普通样式之后添加media query,当media query值为真时可以覆盖掉前面默认的样式,从而实现响应式.在前面样式表的最后添加以下内容

    @media (max-width: 400px) {
      .menu-toggle {
        display: block;
      }
      .menu {
        display: none;
      }
      .menu-active {
        display: block;
      }
      .menu-item {
        float: none;
      }
    }

当屏幕宽度小于400px时菜单会收起,显示列表图标,提示用户可切换.同时取消列表项的浮动,实现竖直菜单栏.

### 添加javascript实现菜单切换

根据前面的思路,现在只需要为菜单切换图标绑定点击事件,为菜单切换`menu-active`类实现菜单开关.js代码如下:


    <script>
    (function () {
      var toggle = document.querySelector('.js-menu-toggle');
      var menu = document.querySelector('.js-menu');

      toggle.addEventListener('click', function (e) {
        menu.classList.toggle('menu-active');
      }, false);
    }());
    </script>

最终得到所需的响应式切换菜单,完整代码可查看[在线demo][2].

[1]: http://iconfont.cn/
[2]: http://qiudeqing.com/demo/mobile_web/responsive-toggle-menu.html
