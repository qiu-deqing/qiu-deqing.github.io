---
title: 列表Key选择
---


React、Vue列表渲染要求设置`key`，某些情况下使用数组索引会带来性能和功能问题。

如果满足一下全部条件，可以使用数组索引

- 列表和内容不变
- 列表项没有id
- 列表不会被重新排序或者筛选（filter)

其他情况下尽量不要使用数组索引。使用业务id或者[Nano ID](https://github.com/ai/nanoid/blob/main/README.zh-CN.md)随机生成唯一id




