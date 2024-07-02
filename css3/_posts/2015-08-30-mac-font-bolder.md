---
title: mac下某些字比设置的粗
---


有时候mac下普通字体会显示得比设置的粗, 如:

![][1]

中的**1天前发布**

给对应元素设置以下属性即可恢复正常:

```
body {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

结果:

![][2]

## 参考资料

- [http://davidwalsh.name/font-smoothing][3]
- [http://szafranek.net/works/articles/font-smoothing-explained/][4]

[4]: http://szafranek.net/works/articles/font-smoothing-explained/
[3]: http://davidwalsh.name/font-smoothing
[2]: https://cloud.githubusercontent.com/assets/5894015/9565389/eef639e8-4efe-11e5-9457-23391065dd65.png
[1]: https://cloud.githubusercontent.com/assets/5894015/9565384/9895e7ce-4efe-11e5-8761-fdccb21b31ea.png
