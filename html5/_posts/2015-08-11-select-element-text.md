---
title: 选择元素文本

---

以下内容只在Chrome和UC手机浏览器上测试过, 其他平台不保证能工作

## 选择input, textarea的全部文本

input, textarea有[select][1]事件, 当文本框内文字被选中时会触发, 调用[select()][2]可以选中元素内全部文本, [在线demo][3]

```
<div id="d1">
  <h2>选择input和textarea的全部文本</h2>
  <input type="text" value="123456789">
  <span class="input-select">点击选择input文本</span>
  <textarea >123456789</textarea>
  <span class="textarea-select">点击选择textarea文本</span>
  <script>
  (function () {
    var container = document.querySelector('#d1');
    var input = document.querySelector('#d1 input');
    var textarea = document.querySelector('#d1 textarea');

    container.addEventListener('click', function (e) {
      var target = e.target;

      if (target.classList.contains('input-select')) {
        input.select();
      } else if (target.classList.contains('textarea-select')) {
        textarea.select();
      }
    }, false);
  }());
  </script>
</div>
```

## 选择input, textarea元素的部分文本

input, textarea具有[setSelectionRange(start, end)][4]方法,可以选择内容的特定子字符串. [在线demo][5]

```
<div id="d2">
  <h2>选择input和textarea的部分文本</h2>
  <input type="text" value="123456789">
  <span class="input-select">点击选择input部分</span>
  <textarea >123456789</textarea>
  <span class="textarea-select">点击选择textarea部分文本</span>
  <script>
  (function () {
    var container = document.querySelector('#d2');
    var input = document.querySelector('#d2 input');
    var textarea = document.querySelector('#d2 textarea');

    container.addEventListener('click', function (e) {
      var target = e.target;

      if (target.classList.contains('input-select')) {
        input.setSelectionRange(3, 5);
      } else if (target.classList.contains('textarea-select')) {
        textarea.setSelectionRange(3, 5);
      }
    }, false);
  }());
  </script>
</div>
```

## 选择普通元素

选择普通文本稍微复杂些,需要[window.getSelection()][6],[document.createRange()][7]然后将元素添加到selection得range中.[在线demo][8]

```
<div id="d3">
  <h2>选择普通元素的文本</h2>
  <p class="para">123 456 789</p>
  <span class="trigger">点击选择段落文本</span>
  <script>
  (function () {

  /**
   * 选择元素的文本
   * @param {Element} 需要被选文本的元素
   **/
  function selectText(elem) {
    var range;
    var selection;

    if (document.body.createTextRange) {
      range = document.body.createTextRange();
      range.moveToElementText(elem);
      range.select();
    } else if (window.getSelection) {
      selection = window.getSelection();
      range = document.createRange();
      range.selectNodeContents(elem);
      selection.removeAllRanges();
      selection.addRange(range);
    }
  }

  var para = document.querySelector('#d3 .para');
  var trigger = document.querySelector('#d3 .trigger');
  trigger.addEventListener('click', function () {
    selectText(para);
  }, false);

  }());
  </script>
</div>
```

[8]: http://qiudeqing.com/demo/html5/select-element-text.html#d3
[7]: https://developer.mozilla.org/en-US/docs/Web/API/Document/createRange
[6]: https://developer.mozilla.org/en-US/docs/Web/API/Window/getSelection
[5]: http://qiudeqing.com/demo/html5/select-element-text.html#d2
[4]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/setSelectionRange
[3]: http://qiudeqing.com/demo/html5/select-element-text.html#d1
[2]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/select
[1]: https://developer.mozilla.org/en-US/docs/Web/Events/select

