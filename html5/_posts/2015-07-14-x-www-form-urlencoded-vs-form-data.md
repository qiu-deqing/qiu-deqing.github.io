---
title: ContentType中application/x-www-form-urlencoded和multipart/form-data的区别
---

需要向服务器发送大量二进制数据或者包含非ASCII字符的文本时. application/x-www-form-urlenoded效率不高, 此时应该使用multipart/form-data


## application/x-www-form-urlencoded

这是[form][1]提交时默认的content type, 编码规则如下:

- 名值对多需要进行转义处理:
  1. 空格使用`+`代替
  2. 保留字按照[RFC1738][2]规则进行转义, 其中非字母,数字的字符被替换为`%HH`格式, HH表示字符在ASCII中得编码
  3. 换行为`CRLF`, 对应序列为`%0D%0A`
- 名值对按照在稳定中出现的顺序列出. name和value使用`=`分隔; 名值对之间使用`&`分隔

表单提交时浏览器会自动根据表单数据进行转义然后发送到服务器.

### 使用ajax向服务器传输数据时如何将数据进行`application/x-ww-form-urlencoded`编码

答: [encodeURIComponment()][3], 但是encodeURIComponent有以下不满足要求的地方:

- 不转换以下字符:
    + `-`
    + `_`
    + `.`
    + `!`
    + `~`
    + `*`
    + `'`
    + `(`
    + `)`
- 使用`%20`替换空格而不是标准的`+`


可以在encodeURIComponent基础上进一步处理:

```
function encodeValue(val) {
  encodedVal = encodeURIComponment(val);
  encodedVal = encodedVal.replace(/[!'()*']/g, function (c) {
    return '%' + c.charCodeAt(0).toString(16);
  });
  return encodedVal.replace(/\%20/g, '+');
}
```

还有就是有的浏览器textarea返回值没有正确使用`CRLF`表示换行,需要进一步处理才能编码

```
value = textarea.value.replace(/\r?\n/g, '\r\n');
```


[4]: http://bugs.jquery.com/ticket/6876
[3]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent
[1]: http://www.w3.org/TR/html5/forms.html#url-encoded-form-data
[2]: http://www.ietf.org/rfc/rfc1738.txt
