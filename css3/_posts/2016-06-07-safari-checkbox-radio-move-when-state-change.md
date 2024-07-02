---
title: Safari下radio和checkbox鼠标hover或者改变选中状态会上下移动
---

原因

项目中对radio和checkbox设置了高度, 导致Safari在状态切换时不能正确计算.造成移动.解决方案: 高度设置回auto

同样原因, 设置宽度之后会导致水平移动

```
input[type=checkbox],
input[type=radio] {
    height: auto;
    width: auto;
}
```
