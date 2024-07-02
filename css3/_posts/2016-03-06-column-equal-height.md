---
title: css实现多列等高
---


设置`padding-bottom`, 用背景色填充矮列, 使用`margin-bottom`向下拉元素, 造成多列等高的假象

```
<style>
.p {
    overflow: hidden;
    border: 1px solid #000;
}
.c {
    float: left;
    width: 100px;
    border: 1px solid #ddd;
    padding-bottom: 2000px;
    margin-bottom: -2000px;
}
</style>


<div class="p">
    <div class="c">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Aliquam blanditiis reiciendis reprehenderit, eius, animi quod consequuntur, deserunt ex magnam inventore ut commodi iusto alias voluptatum fugiat. Earum maxime optio eveniet.</div>
    <div class="c"></div>
    <div class="c"></div>
</div>

```
