---
title: Unicode和UTF基础
---


# Unicode

Unicode是一个字符集, 为每一个字符提供一个唯一的编号, 由[Unicode Consortium][2]维护.


详细信息参考[wikipedia: Unicode][3], [http://www.unicode.org/][4]

# code point

Unicode为字符提供的唯一编号叫做`code point`或者`code position`.

# Unicode Plane

在Unicode标准中, 一个[plane][5]由连续的**65536(2的十六次方)** 个code point组成.

一共有17个plane, 位置编码的前两位表示plane编码, 即**hh hhhh**的前两位.

plane 0 叫做[Basic Multilingual Plane][6], 其余的plane叫做Supplementary plane

# Basic Multilingual Plane

BMP几乎包括现代语言的所有字符和大量图标. 其中的大部分code point用于编码[CJK字符][7]


# UTF

Unicode Transformation Format(UTF)是用于将每一个Unicode code point映射为一个唯一的字节序列的编码算法. 常见的编码算法有UTF8, UTF-16, UTF-32.

# code unit

编码算法用于表示字符的最小bit长度, 如UTF8的code unit是8, UTF16的code unit是16

# UTF8

[UTF8][8]的code unit为8 bit



# 计算字符串在utf8编码的字节长度

JavaScript字符串是utf16编码, 常用的charAt(), getCharCode(), length都是基于utf16的code unit, 当字符在Unicode的Basic Multilingual Plane内时都可以正常工作,
计算utf8编码长度需要使用基于code point的函数, 自己手动实现编码转换肯定不现实, 应该使用对应的字符串处理库.

正则表达式也是基于code unit的.

平时我们用这些方法都没什么问题是因为平时接触到的字符都在Basic Multilingual Plane下面, [Effective JavaScript](http://www.amazon.com/gp/product/0321812182/ref=s9_simh_gw_g14_i2_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-1&pf_rd_r=13FW4ZZT7X3ED06YGY7T&pf_rd_t=36701&pf_rd_p=2079475242&pf_rd_i=desktop)中第七条: **Think of Strings As Sequences of 16-bit code units**有详细解释, 整本书也比较不错. 电子版我放到了附件中, 整本书没有废话, 都是干活, 强烈推荐

浏览器下的encodeURI, decodeURI, encodeURIComponent, decodeURIComponent刚好就是以code point工作的, 我们使用encodeURIComponent

因为encodeURIComponent不转义: 字母 数字 - _ . ! ~ * ' ( ) 需要把这部分先提取出来, 计算长度,

剩下的转义, 计算长度


```
var util = {
    /**
     * 计算字符串在utf8编码的字节长度
     * @param str {string} 需要计算字节长度的字符串
     * @return {number}  字符串在utf8编码下的字节长度
     **/
    utf8ByteLength: function (str) {
        str = str || ''
        var plainReg = /[\w-\.!~*'\(\)']/g
        var utf8TokenReg = /%\w{2}/g
        var plainTokens = str.match(plainReg) || []
        var complexStr = str.replace(plainReg, '')
        var encodedStr = encodeURIComponent(complexStr)
        var utf8Tokens = encodedStr.match(utf8TokenReg) || []
        return plainTokens.length + utf8Tokens.length
    }
}


console.log(util.utf8ByteLength('a'))   // 1
console.log(util.utf8ByteLength('¢'))   // 2
console.log(util.utf8ByteLength('中'))   // 3
console.log(util.utf8ByteLength('𐍈'))   // 4
```


[9]: http://illegalargumentexception.blogspot.jp/2010/12/javascript-validating-utf-8-string.html
[8]: https://en.wikipedia.org/wiki/UTF-8
[7]: https://en.wikipedia.org/wiki/CJK_characters
[6]: https://en.wikipedia.org/wiki/Plane_(Unicode)#Basic_Multilingual_Plane
[5]: https://en.wikipedia.org/wiki/Plane_(Unicode)
[4]: http://www.unicode.org/
[3]: https://en.wikipedia.org/wiki/Unicode
[2]: http://www.unicode.org/consortium/consort.html
[1]: http://www.unicode.org/standard/WhatIsUnicode.html
