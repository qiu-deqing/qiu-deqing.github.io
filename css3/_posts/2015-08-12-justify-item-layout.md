---
title: "[css 布局]容器左右间距固定,子元素靠左右边界停靠且平分多余空间"

---

导航栏常用这种布局, 常见于导航栏

![][1]

## text-align实现

可以兼容比较老的浏览器


实现步骤:

1. 父元素设置所需的左右间距, `text-align: justify`
2. 子元素设置`display: inline-block;`, 或者可能需要修复text-align
3. 设置父元素`::after`为最后一行添加强制换行

```
<style>
.grid {
  background: #ddd;
  padding: 0 100px;
  width: 500px;

  text-align: justify;
}
.grid::after {
  content: '';
  display: inline-block;
  width: 100%;
}
.grid-item {
  background: purple;
  width: 30%;
  height: 50px;
  margin-bottom: 50px;
  display: inline-block;
}
</style>
<div class="grid">
  <div class="grid-item">111</div>
  <div class="grid-item">222222222222222</div>
  <div class="grid-item">333</div>
  <div class="grid-item">444444444444444</div>
  <div class="grid-item">333</div>
  <div class="grid-item">333</div>
  <div class="grid-item">333</div>
  <div class="grid-item">444444444444444</div>
</div>
```

## display: flex;实现

如果不需要考虑IE9及之前的版本,这个方法简单方便

1. 设置父容器`display: flex;`
2. 设置父容器`justify-content: space-between;`
3. 如果允许子元素过多分布到多行,设置父容器`flex-wrap: wrap;`

```
<style>
.grid {
  background: #ddd;
  padding: 0 10px;

  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.grid-item {
  background: purple;

  height: 50px;
  padding: 0 20px;
  margin-bottom: 50px;
}
</style>
<div class="grid">
  <div class="grid-item">3333</div>
  <div class="grid-item">3333</div>
  <div class="grid-item">3333</div>
  <div class="grid-item">3333</div>
  <div class="grid-item">3333</div>
  <div class="grid-item">3333</div>
  <div class="grid-item">3333</div>
</div>
```


[1]: https://cloud.githubusercontent.com/assets/5894015/9219427/6b394108-410d-11e5-8798-bdc32741c9a4.png
