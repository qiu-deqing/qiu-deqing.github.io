---
title: "[译]anchor， input， button实现按钮的区别"
---

原文链接: [http://davidwalsh.name/html5-buttons][1]


使用CSS和JavaScript可以让三种元素生成的内容样式和行为完全一致。他们的区别是什么，什么时候用什么？

## 语义(Semantics)

### Anchors

Anchor(`<a>`)表示超链接，通常表示导航和下载功能，如果需要用户导航到其他页面或者触发下载，应该使用它。

### Inputs

Input(`<input>`)表示一个数据域，通过`type`属性高数浏览器它包含的数据。以下是与按钮相关的五种类型：

 - `<input type="submit">`：这是最常用的类型，点击按钮会提交表单
 - `<input type="image">`：点击也会提交表单，同时接受`src`属性指定显示图片
 - `<input type="file">`：用于选择文件，显示为一个按钮和对应label，很难实现不同浏览器下样式的统一，通常会选择`visibility: hidden;`然后用其它元素设置样式，监听点击事件触发弹窗。
 - `<input type="reset">`：点击时重置表单
 - `<input type="button">`: 点击无默认行为的按钮,可以通过JavaScript指定所需行为.

 ### Buttons

`<button>`元素表示一个按钮, 比input更容易控制样式.button的label由内容决定. 可以在button里面嵌套图像,段落或者标题. button可以包含`::before`和`::after`伪元素.

和input元素一样button有`type`属性,可以取值为`submit`,`reset`或者`button`并且与input对应类型有同样的行为.**默认行为是`submit`**

**input和button一个有用的功能是支持`disabled`属性**.这样就很方便开关它们, 链接没有这个功能

## 应该如何选择呢?

- 当需要将用户导航到其他页面或者资源时,使用`<a>`,其他情况下button和input都可以
- 个人偏好提交和重置表单时使用input
- 自定义自定义行为时使用button



[1]: http://davidwalsh.name/html5-buttons
