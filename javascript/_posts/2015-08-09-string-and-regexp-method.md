---
title: String和RegExp匹配相关方法

---

## 快速总结

- [RegExp.prototype.test(str)][1]: 返回值`true`或者`flase`. 检测str中是否包含regexp模式. **如果regexp中设置了`g`属性,执行比较是从`regexp.lastIndex`开始并且每次比较都会修改lastIndex**
- [String.prototype.search(regexp)][3]: 返回值`-1`或者匹配的起始下标.

- [RegExp.prototype.exec(str)][2]: 返回数组或者null. 在str中匹配模式并且返回匹配信息.返回的数组增加了`index`属性,表示匹配的起始下标`input`为进行测试的字符串. **如果regexp设置了`g`属性,执行比较是从`regexp.lastIndex`开始并且每次都会修改lastIndex**

    1. result[0]为匹配的子字符串
    2. result[1, 2, 3....n]为匹配的多个分组

- [String.prototype.match(regexp)][4]: 返回数组或者null.

    1. 如果regex没设置`g`, 返回结果与RegExp.exec()一致.
    2. 如果设置了`g`, 返回结果为所有匹配的子字符串, 分组信息不会返回



[4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match
[3]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/search
[2]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec
[1]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test
