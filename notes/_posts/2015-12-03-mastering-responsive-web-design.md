---
title: Mastering Responsive Web Design笔记
---

![][1]

[笔记代码][6]

# 第一章 利用SASS特性更好地实现响应式web

- 安装ruby版本的sass
- 目录下创建`scss`, `css`两个目录, 执行命令`sass --watch scss:css`会监控scss文件修改并编译到css目录


## Sass基本概念

### Sass vs SCSS

Sass有Sass和SCSS两种语法.

Sass语法也叫做缩进语法, 是最初的Sass语法, 不使用任何的括号和分号, 缩进规则非常严格并且是强制的.

Sass 第三版引入了SCSS语法, 在CSS的基础上进行了增强

### Sass变量

Sass变量必须以`$`符号开头, 修改变量值将替换所有使用到这个变量的值. 变量后面的`;`用于分隔多个变量声明.

变量声明应该放到SCSS文件的开头

```
$brandBlue: #aaa;
$brandColor: #eee;

body {
  color: $brandColor;
}
```

### Sass mixins

Mixin是一系列可以服用的CSS声明, 类似于变量.

- 以`@mixin`指令开头
- 使用`@include`指令调用
- 通过参数可以是mixin更加强大

```
$brandBlue: #333;
$supportGray: #ccc;

@mixin genericContainer {
  padding: 10px;
  border: $brandBlue 1px solid;
  background: $supportGray;
  box-shadow: 1px 1px 1px rgba(black, 0.5);
}

.selector-a {
  @include genericContainer;
}
```

### Sass参数

通过参数使mixin更加强大. 支持默认参数

```
$brandBlue: #ddd;
$supportGray: #ccc;
@mixin genericContainer($padding: 8px, $bdColor: orange) {
  padding: $padding;
  border: $brandBlue 1px solid;
  background: $bdColor;
  box-shadow: 1px 1px 1px rgba(black, 0.3);
}

.selector-a {
  @include genericContainer(10px);
}
.selector-b {
  @include genericContainer($bdColor: $brandBlue);
}
```

### 嵌套

像HTML一样嵌套选择器, 生成对应嵌套选择器.

```
$brandBlue: #ddd;

nav {
  ul {
    display: flex;
    color: $brandBlue;
  }
}
```

### partial

partial是一小段SCSS代码, 方便我们实现模块化, 如`_variables.scss`. partial以下划线开头, `.scss`为后缀. 下划线告诉编译器不要把文件编译为单独的css.

使用`@import`指令引入partial, 不需要指定下划线和文件后缀名.

_variables.scss:

```
$brandBlue: #416e8e;
$brandRed: #c03;
$brandYellow: #c90;
```

在styles.scss中引入_variables.scss:

```
@import "variables";
```

### extend/inherit

extend允许你直接使用另一个选择器的属性. 通过`@extend`指令完成.

```
$brandBlue: #ddd;
.generic-container {
  padding: 10px;
  border: $brandBlue 1px solid;
}

.box-customer-service {
  @extend: .generic-container;
  padding: 25px;
}
```

输出结果:

```
.generic-container, .box-customer-service {
  padding: 10px;
  border: #ddd 1px solid;
}

.box-customer-service {
  padding: 25px;
}
```

### Sass注释

`//`注释编译之后不输出到css

`/**/`注释编译之后输出到css

### 浏览器前缀

- `-moz-` Mozilla
- `-o-` Opera
- `-ms-` Microsoft
- `-webkit-` Webkit(Apple)
- `-webkit-` Chrome

Chrome最初使用Webkit内核, 后来改用Blink, 但是还是使用`-webkit-`

Opera最初使用Presto,然后Webkit,然后Blink

可以使用[Compass][2]处理前缀.

也可以用[-prefix-free][3] 但是都不好.

**Autoprefixer**根据[can i use][4]数据库信息添加必须的前缀


## Sass mixin与media query

_mediaqueries.scss

```
@mixin minw($point) {
  @if $point == small {
    @media (min-width: 20em) { @content; }
  }
  @else if $point == medium {
    @media (min-width: 40em) { @content; }
  }
  @else if $point == large {
    @media (min-width: 47.5em) { @content; }
  }
}
```

styles.scss

```
header {
  width: 50%;
  background: red;
  @include minw(640) {
    width: 100%;
    background: blue;
  }
}
```

styles.css

```
header {
  width: 50%;
  background: red;
}
@media (min-width: 40em) {
  header {
    width: 100%;
    background: blue;
  }
}
```

定义media query断点单位为**em**, 这样不依赖于屏幕density.

# 第二章 使用HTML5编写内容

- HTML5标签
- 使用WAI-ARIA(Web Accessibility Initiative - Accessible Rich Internet Applications) role增强可访问性
- RWD需要的meta标签
- 一个完整的使用了ARIA role熟悉和meta标签的页面

## <main>标签

`<main>`可以用来作为包含文档主要内容的容器.  内容必须是唯一的, 不能包含sidebar, navigation, copyright information , site logo, 搜索框, main不会邮箱文档大纲.

- 页面顶级内容必须包含在<main>中
- 内容必须唯一
- 不能包含在以下标签中: <header>, <footer>, <nav>, <aside>, <article>
- 一个页面只能有一个main元素

```
<body>
  <main class="main-container" role="main">
    Content goes here
  </main>
</body>
```

## <article>

`<article>`表示文档中一个字包含部分, 可以独立发布或者复用, 如论坛回复, 杂志文章. 每个<articel>应该有自己的标题(h1-h6)

- 所有独立内容应该放到`<article>`中. article不依赖于它所处的位置, 放到任何一个地方都可以自解释
- 一个`<article>`可以嵌套在另一个<article>中
- 一个页面中可以有多个<article>

```
<body>
  <main class="main-container" role="main">
    <article class="article-container flex-container">
      Content goes here
    </article>
  </main>
</body>
```

## <section>

`<section>`表示文档中得一个区域, 区域内通常包含自己的标题(h1-h6)

- 可以用`<section>`封装一组相关内容.
- 通常在`<article>`内使用<section>
- 不确定使用<article>还是<section>时, 两者都行
- 一个页面可以有多个<section>

```
<body>
  <main class="main-container" role="main">
    <article class="article-container flex-container">
      <section class="main-content">
        <header>
          <h1>here is title</h1>
        </header>
        <p>As per the MDN</p>
        <blockquote>
          <p>The HTML Main element</p>
        </blockquote>
      </section>
    </article>
  </main>
</body>
```

## <aside>

表示与文档内容无关的内容. 如广告.

- 一个页面可以有多个<aside>

```
<body>
  <main class="main-container" role="main">
    <article class="article-container flex-container">
      <section class="main-content">
      </section>
      <aside class="side-content" role="complementary">
        <h2>What does aaa mean</h2>
        <p>hello</p>
      </aside>
    </article>
  </main>
</body>
```

## <header>

<header>表示一组介绍或者导航信息. 可能包含标题, logo, 搜索框等.

- 通常在<section>中使用<header>
- 一个页面可以有多个<header>

```
<body>
  <header class="masthead" role="banner">
    <div class="logo">Matering RWD</div>
    <div class="search" role="search">
      <form>
        <label>Search:
          <input type="text">
        </label>
      </form>
    </div>
  </header>
  <main class="main-container" role="main">
    <article>
      <section>
        <header>
          <h2>Header</h2>
        </header>
      </section>
    </article>
  </main>
</body>
```

## <footer>

<footer>包含所在区域的附加信息. 通常包含作者, copyright, 相关链接等信息.

- 应该用来包含所在区域信息
- <footer>不是必须在底部
- 一个页面中可以包含多个<footer>

```
<body>
  <main>
    <article>
    </article>
    <footer>
      <p>copyright</p>
      <ul>
        <li><a href="#">link1</a></li>
        <li><a href="#">link2</a></li>
      </ul>
    </footer>
  </main>
</body>
```

## <nav>

<nav>用于包含导航链接

- 包含的链接可以是外部资源, 也可以是本页资源
- 一个页面可以有多个<nav>

## viewport

Apple在移动版Safari浏览器引入`viewpport`属性, 通常设置如下:

```
<meta name="viewport" content="width=device-width, initial-scale=1">
```

- `name="viewport"`表示设置的类型
- `content="width=device-width, initial-scale=1`设置内容
- 不推荐设置`maximum-scale=1, user-scaleable=no`限制用户操作

## charset

```
<meta charset="utf-8">
```


[6]: https://github.com/qiu-deqing/mastering-responsive-web-design-code
[5]: https://prepros.io/
[4]: http://caniuse.com/
[3]: http://leaverou.github.io/prefixfree/
[2]: http://compass-style.org/
[1]: http://gtms04.alicdn.com/tps/i4/TB1C9hjKVXXXXbsXpXXNuAVQVXX-405-500.jpg

