---
title: 使用box-sizing控制元素宽高
---

css默认宽高是内容的宽高,有border,padding的时候计算很麻烦.

`box-sizing: border-box;`设置元素宽度高度设置值为边框所在盒子,方便设置.


IE8支持`box-sizing`[can is use box-sizing][1]


推荐的设置:

```
html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
```



[1]: http://caniuse.com/#feat=css3-boxsizing
