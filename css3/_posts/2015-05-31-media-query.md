---
title: css3 media query
---

# CSS3 media querise

一个媒体查询（media query）包含**一个媒体类型（media type）**和**至少一个表达式**，用媒体特性限制样式表的作用范围。


## 语法

媒体查询包含一个[媒体类型(media type)][]以及一个到多个测试[媒体特性(media feature)][]的表达式，表达式和媒体类型将根据实际情况计算的到`true`或者`false`。如果**指定的媒体类型符合当前设备**并且**媒体特性表达式都为真**，那当前媒体查询为真。

当一个media query为true时，对应的样式表按照正常样式规则生效。指定了media query的`<link>`中的样式始终会下载。

**除非使用了`not`或者`only`操作符，media type是可选的，默认值为`all`。**

## 逻辑运算符

### and：用于结合多个媒体特性(media feature)到一个media query

只有所有feature表达式为真且满足媒体类型时整个media query才为真。

以下是一个简单media query，用于检测media type为all时的一个media feature：

    @media (min-width: 700px) {}

当我们需要添加限制条件是，可以使用and完成目的如下：

    @media (min-width: 700px) and (orientation: landscape) {}

上面的media query只有在viewport大于700px并且width &bt; height时生效。此时如果需要限制媒体类型为彩色电脑显示器，可以使用and添加media type如下：

    @media screen and (min-width: 700px) and (orientation: landscape) {}

### ,逗号运算符：用于结合多个media  query，任一media query为true时应用样式。

逗号运算符相当于逻辑运算符中的or。逗号分隔的每一个media query都需要进行单独求值，使用在某一个media query上的其他运算符不会影响到其他media query。

如果需要在大于700px宽的所有设备或者宽度大于高度的彩色电脑屏幕上应用样式，可以使用如下规则：

    @media (min-width: 700px), screen and (orientation: landscape) {}

### not：用于对整个media query结果取反,必须位于一个media query的开头

在逗号分隔的多个media query中，not只对它作用的media query生效。**not不能对单个media feature取反，只能作用于整个media query**

例1：如下面的**not将在最后求值**：

    @media not all and (monochrome) {}

等价于下面的query：

    @media not (all and (monochrome)) {}

例2：下面的多个media query求值

    @media not screen and (color), print and (color) {}

求值顺序如下：

    @media (not (screen and (color))), print and (color) {}

### only：用于向早期浏览器隐藏媒体查询，only必须位于media  query的开头


    @media only screen and (min-width: 400px) and (max-width: 600px) {}

无法识别媒体查询的浏览器要求获得**逗号分割的媒体类型列表**，规范要求：它们应该在**第一个不是连字符的非数字、字母之前截断每个值**。所以上面的示例解释为：

    @media only {}

因为没有only这样的媒体类型，所以样式表被忽略。如果不加only，下面的示例

    @media screen and (min-width: 400px) and (max-width: 600px) {}

被解析为`@media screen {}`这样一来**即使浏览器不知道media query的真正含义，样式也会应用于所有屏幕设备**。不幸的是，IE6-8未能正确实现该规范。没有将样式应用到所有屏幕的设备，它将整个样式表忽略掉。

尽管存在此行为，如果希望向其他不太常用的浏览器隐藏样式，任然建议在媒体查询前面添加`only`。



## 媒体类型（media type）

- all：适用与所有设备
- print：paged material  and documents viewed on screen in print previe mode.
- screen: 彩色电脑显示器
- speech：intended for speech synthesizers

注意：CSS2.1和CSS3 media query定义了`tty, tv, projection, handheld, braille, embossed, aural`这些media type，但是它们在[media queries 4][] 中都废弃了，所以不推荐使用了


## 媒体特性（media feature）

下面是一些media feature，不是全部

- width： viewport width
- height: viewport height
- aspect-ratio: viewport的宽高比如：16/9
- orientation: 宽度和高度的大小关系。。
- resolution: pixel density of the output device
- scan: scanning process of the output device
- grid: is the device a grid or bitmap
- color: number of bits per color component of the output device, or zero if the device isn't color
- color-index: number of entries in the output device's color lookup table, or zero if the device does not use such a table



[媒体类型(media type)]: https://developer.mozilla.org/en-US/docs/Web/CSS/@media
[媒体特性(media feature)]: https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries#Media_features
[media queries 4]: http://dev.w3.org/csswg/mediaqueries/#media-types


## media query常用方法

### 排他（exclusive）

为确保在某一个条件下只有一个样式表生效，将查询条件严格划分，如下面：

    @media (max-width: 400px) {
        html {
            background: red;
        }
    }

    @media (min-width: 401px) and (max-width: 800px) {
        html {
            background: green;
        }
    }

    @media (min-width: 801px) {
        html {
            background: blue;
        }
    }

### 覆盖（overriding）

可以对元素设置相同优先级，使用样式顺序，通过覆盖，避免排他

    @media (min-width: 400px) {
        html {
            background: red;
        }
    }

    @media (min-width: 600px) {
        html {
            background: green;
        }
    }

    @media (min-width: 800px) {
        html {
            background: blue;
        }
    }

### 无线端优先（Mobile first）

默认样式假设为移动设备宽度，然后通过`min-width`控制扩展样式

    html {
        background: red;
    }

    @media (min-width: 600px) {
        html {
            background: green;
        }
    }

### PC优先（desktop first）

默认以宽屏进行样式设置，通过`max-width`控制样式覆盖

    html {
        background: red;
    }

    @media (max-width: 600px) {
        html {
            background: green;
        }
    }

## 参考资料

- [MDN:CSS media queries][]
- [introducing media queries][]
- [logic in meida queries][]




[MDN:CSS media queries]: https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries
[introducing media queries]: http://www.adobe.com/cn/devnet/dreamweaver/articles/introducing-media-queries.html
[logic in meida queries]: http://css-tricks.com/logic-in-media-queries/
