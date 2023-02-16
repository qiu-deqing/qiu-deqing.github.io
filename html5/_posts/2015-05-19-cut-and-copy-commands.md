---

title: "[译]剪切和复制命令"

---

# 剪切和复制命令

[原文链接](http://updates.html5rocks.com/2015/04/cut-and-copy-commands)

IE10增加了通过[Document.execCommand()][cnc-1]执行剪切,复制命令的功能.Chrome 43也支持这些命令.

浏览器中任意选中的文本都可以剪切或者复制到剪切板.

当这个功能与[Selection API][cnc-2]结合使用可以达到很好的效果.

## 简单例子

本例将实现点击按钮复制邮件地址功能.[demo][cnc-3]

    <div id="demo1">
      <p>Email me at <a href="mailto:matt@example.co.uk" class="js-emaillink">matt@example.co.uk</a></p>

      <p><button class="js-emailcopybtn"><img src="http://7xio0w.com1.z0.glb.clouddn.com/avatar.jpg" alt=""></button></p>

      <script>
      (function () {
        var copyEmailBtn = document.querySelector('.js-emailcopybtn');
        copyEmailBtn.addEventListener('click', function (event) {

          // select the email link anchor text
          var emailLink = document.querySelector('.js-emaillink');
          var range = document.createRange();
          range.selectNode(emailLink);

          window.getSelection().addRange(range);

          try {
            // now that we've selected the anchor text, execute the copy command
            var successful = document.execCommand('copy');
            var msg = successful ? 'successful' : 'unsuccess';
            console.log('copy email command was ' + msg);
          } catch (erro) {
            console.log('unable to copy');
          }

          // Remove the selections - Note: Should use
          // removeRange(range) when it is supported
          window.getSelection().removeAllRanges();
        });
      }());
      </script>

在javascript中为按钮添加了时间监听器,监听器选取`js-emaillink`链接的邮件地址文本,然后执行复制命令将邮件地址复制到用户的剪切板,最后取消邮件地址的选中状态.

在上面的代码中我们使用了[Selection API][cnc-2], [window.getSelection()][cnc-4]来设置链接文本的选取,这正是我们需要复制到剪切板的内容.在执行`document.execCommand()`之后我们通过调用[window.getSelection().removeAllRanges()][cnc-5]解除文本选择状态.

如果需要确定一切都按照预期正常工作,可以检查`document.execCommand()`返回值,当命令不支持或者被禁止时将返回`false`.我们将`execCommand()`调用包装在try-catch中是因为剪切和复制在某种情况下会[抛出错误][cnc-6].


**剪切**命令可以在文本输入框中的文本.[demo][cnc-8]


    <div id="demo2">
      <p><textarea class="js-cuttextarea">Hello I'm some text</textarea></p>
      <p><button class="js-textareacutbtn" disable>Cut Textarea</button></p>

      <script>
      (function () {
        var cutTextareaBtn = document.querySelector('.js-textareacutbtn')

        cutTextareaBtn.addEventListener('click', function (event) {
          var cutTextarea = document.querySelector('.js-cuttextarea');
          cutTextarea.select();

          try {
            var successful = document.execCommand('cut');
            var msg = successful ? 'successful' : 'unsuccessful';
            console.log('Cut text is ' + msg);
          } catch (e) {
            console.log('cut error');
          }
        }, false);
      }());
      </script>
    </div>


## 参考资料

- [http://updates.html5rocks.com/2015/04/cut-and-copy-commands]()


[cnc-1]: https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand
[cnc-2]: https://developer.mozilla.org/en-US/docs/Web/API/Selection
[cnc-3]: http://qiudeqing.com/demo/html5/cut-and-copy-commands.html#demo1
[cnc-4]: https://developer.mozilla.org/en-US/docs/Web/API/Window/getSelection
[cnc-5]: https://developer.mozilla.org/en-US/docs/Web/API/Selection/removeAllRanges
[cnc-6]: https://dvcs.w3.org/hg/editing/raw-file/tip/editing.html#the-copy-command
[cnc-7]: https://developer.mozilla.org/en-US/docs/Web/API/Document/queryCommandSupported
[cnc-8]: http://qiudeqing.com/demo/html5/cut-and-copy-commands.html#demo2

