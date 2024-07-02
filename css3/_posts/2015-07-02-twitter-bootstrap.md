---
title: twitter bootstrap笔记
---

## component

### dropdown

![][3]下拉菜单[dropdown][1]三要素

- `position: relative;`的容器用于包含触发按钮和菜单内容(建议使用官方提供的`.dropdown`)
- 为菜单切换按钮设置`data-toggle="dropdown"`用于告诉BS这是切换按钮
- 设置`class="dropdown-menu"`的菜单

bootstrap会自动寻找到带有data-toggle属性的元素然后绑定点击事件, 为该元素父节点切换`open`类

`.dropdown-menu`为绝对定位


```
<!-- .dropdown 提供position: relaitve;为子菜单包含块 -->
<div class="dropdown">

  <!-- 菜单切换按钮 -->
  <!-- data-toggle="dropdown" Bootstrap寻找这个属性,值的元素, 绑定点击时间,为父元素 -->
  <!-- 切换.open, 控制子菜单显示 -->
  <button type="button" data-toggle="dropdown" class="btn btn-default">
    Dropdown
    <span class="caret"></span>
  </button>

  <!-- .dropdown-menu 样式和菜单控制必备 -->
  <ul class="dropdown-menu">
    <li><a href="">1</a></li>
    <li><a href="">2</a></li>
    <li><a href="">3</a></li>
  </ul>
</div>

```


### navbar

![][4]页面导航栏[navbar][2]通常用作整个页面或者网站的导航, 根据屏幕自适应, 以下是要素:

- 导航栏容器设置`.navbar`, 通常选`<nav>`元素作为导航栏容器
- 一个导航栏头部`.navbar-header`包含始终显示的站点主要信息和窄屏幕下导航栏切换按钮
- 导航栏切换按钮需要具有`.navbar-toggle`,`data-target="selector"`,`data-toggle="collapse"`其中data-target的selector是点击当前按钮所切换的导航内容选择器
- 包含站点主要信息的元素需要使用`.navbar-brand`控制样式
- 导航内容容器需要具备`.collapse`以实现窄屏幕自动隐藏,`.navbar-collapse`设置样式.以及可用于导航栏切换的标识
- 导航内容容器中的元素通常具有`navbar-*`一系列类用于设置样式

一下是一个demo

```

<!-- .navbar:导航标识  .navbar-default: 主题 -->
<nav class="navbar navbar-default">

  <!-- .navbar-header: 导航头,站点主要信息和切换按钮 -->
  <div class="navbar-header">

    <!-- 这个按钮在宽屏下隐藏,窄屏下作为导航项的切换触发器 -->
    <!-- data-toggle="collapse": Bootstrap寻找具有这个属性和值的元素绑定点击事件进行切换 -->
    <!-- data-target=".qiu" 属性值为导航体的选择器, 点击本按钮会为导航体切换状态 -->
    <!-- .navbar-toggle 样式所必须 -->
    <!-- .collpsed 本按钮宽屏隐藏, 窄屏显示 -->
    <button type="button" data-toggle="collapse" data-target=".qiu" class="navbar-toggle collapsed">
      <span class="sr-only">Toggle navigation</span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
      <!-- .icon-bar 窄屏下按钮样式 -->
    </button>

    <!-- .navbar-brand 样式,必不可少,网站主要名称,始终显示,通常链接到首页 -->
    <a href="#" class="navbar-brand">Brand</a>

  </div>

  <!-- 导航体, 里面包含导航项 -->
  <!-- .collapse: 窄屏下内容自动隐藏 -->
  <!-- .navbar-collapse 样式,已经隐藏相关 -->
  <div class="collapse navbar-collapse qiu">

    <!-- 一个导航单元 -->
    <!-- .nav  nav部分样式 -->
    <!-- .navbar-nav 导航必备样式 -->
    <ul class="nav navbar-nav">

      <!-- .active当前项 -->
      <li class="active"><a href="#">Link</a></li>

      <!-- 普通项 -->
      <li><a href="#">Link2</a></li>

      <!-- 也可以放复杂下拉菜单, 按照下拉菜单设置即可 -->
      <li class="dropdown">

        <!-- 下拉菜单在这里只能用a标签作为切换按钮 -->
        <a href="#" data-toggle="dropdown">
          dropdown
          <span class="caret"></span>
        </a>

        <ul class="dropdown-menu">
          <li><a href="#">1</a></li>
          <li><a href="#">2</a></li>
          <li><a href="#">3</a></li>
        </ul>
      </li>

    </ul>

    <!-- 导航栏常见搜索功能 -->
    <!-- .navbar-form 与.form-inline具有相似样式 -->
    <!-- .navbar-left 导航项向左浮动 -->
    <form class="navbar-form navbar-left">

      <!-- 参照.form-inline设置即可,下同 -->
      <div class="form-group">
        <input type="text" placeholder="Search" class="form-control">
      </div>

      <button type="submit" class="btn btn-default">Submit</button>

    </form>

    <!-- 设置一个靠右的导航项,规则和普通导航一样 -->
    <ul class="nav navbar-nav navbar-right">

      <li><a href="#">Link</a></li>

      <li class="dropdown">

        <a href="E" data-toggle="dropdown">
          Dropdown
          <span class="caret"></span>
        </a>

        <ul class="dropdown-menu">
          <li><a href="">1</a></li>
          <li><a href="">2</a></li>
          <li><a href="">3</a></li>
        </ul>
      </li>

    </ul>

  </div>

</nav>

```

[4]: https://cloud.githubusercontent.com/assets/5894015/8481115/007b0234-2114-11e5-8e5c-f0e92f7c30a3.png
[3]: https://cloud.githubusercontent.com/assets/5894015/8481098/e73adf56-2113-11e5-8fc4-c979eacd70ba.png
[2]: http://getbootstrap.com/components/#navbar
[1]: http://getbootstrap.com/components/#dropdowns
