---
title: 响应式web
---

## 目标

在任意设备上灵活展示页面, 最小化水平滚动和缩放

## <meta>标签

## 图片

### iconfont

- 尺寸灵活, 不失真
- 文件小
- 只适合纯色

### 普通图片

- 设置最大宽度,高度自动

```
max-width: 100%;
height: auto;
```

### 背景图片

- 支持双倍图

  ```
  background-image: url("//gtms03.alicdn.com/tps/i3/TB1LoVGKpXXXXXOXFXXXWVYJXXX-79-84.png");
  background-image: -webkit-image-set(
                      url("//gtms03.alicdn.com/tps/i3/TB1LoVGKpXXXXXOXFXXXWVYJXXX-79-84.png") 1x,
                      url("//gtms04.alicdn.com/tps/i4/TB1U8XoKpXXXXcUXVXX0gTDPpXX-158-168.png") 2x
                    );
  ```

- 宽度变化高度不能自适应: 1. rem 2. background-size

  ```
  width: 3rem;
  height: 2rem;
  backgrond-size: 100% 100%;
  ```

## Sass mixin方便media query

- 同一个地方维护PC和无线样式, 方便查找, 维护.
- 抽象名字更方便理解
- 只需要在_mediaqueries.scss修改断点

_mediaqueries.scss

```
@mixin mq($point) {
  @if $point == sm {
    @media (min-width: 20em) { @content; }
  }
  @else if $point == md {
    @media (min-width: 40em) { @content; }
  }
  @else if $point == lg  {
    @media (min-width: 48em) { @content; }
  }
}
```

styles.scss

```
@import "mediaqueries";

header {
  width: 50%;
  background: red;

  @include mq(sm) {
    width: 100%;
    background-color: blue;
  }
}

```

styles.css

```
header {
  width: 50%;
}

@media (min-width: 40em) {
  header {
    header: 100%;
  }
}
```

[1]: https://davidwalsh.name/responsive-images
