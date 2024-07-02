---
title: sublime text3基本使用及常用插件介绍

---

## 概述

sublime text3下载地址：[http://www.sublimetext.com/3][2]本文所有操作都是基于Sublime Text3，操作系统为mac，本文主要摘取自[Sublime Text Unofficial Documentation][1]。熟练掌握各种功能和技巧，能极大提高工作效率。

[2]: http://www.sublimetext.com/3
[1]: http://docs.sublimetext.info/en/latest/index.html

[快捷键集合][01]

## 快捷键

- `Cmd + shift + d`: 复制并粘贴当前选中内容, 如果没选中,复制粘贴光标所在行

## 安装

- 官网下载`.dmg`文件
- 打开`.dmg`文件
- 拖拽Sublime Text 3到Applications文件夹

如果想要在命令行中启动Sublime Text，需要在终端执行一下命令：

    ln -s  "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/subl

这样设置之后在终端执行`subl`即可打开Sublime Text，执行`subl <filepath>`可以打开对应文件。

## 基本概念

熟悉本节介绍的概念能让你更好地理解本教程。

### The Data Directory

几乎所有需要的文件都会保存在data directory下，不同操作系统路径不同：

- Windows: `%APPDATA%\Sublime Text 3`
- OS X: `~/Library/Application Support/Sublime Text 3`
- Linux: `~/.config/sublime-text-3`

可以通过菜单:(Sublime Text -> Preferences -> Browse Packages...)在findle中打开该目录的子目录。

### The Packages Directory

这是data directory下的一个重要目录。关于编程和标记语言的所有支持都保存在这里。可以通过菜单:**(Sublime Text -> Preferences -> Browse Packages...)**在findle中打开该目录。

本文中Packages, packages path, packages folder或者packages directory都指它。

### The User Package

`Packages/User`用于保存自定义插件(plugins)，snippets,宏(macros)。可以将它看作packages folder下的私人区域。个人程序和插件设置都存放在这里。

Sublime Text更新时不会修改里面的内容。

### Sublime Text是可编程的

可以通过API使用Python开发插件。 快捷键``Ctrl + ` ``打开控制台，可以在这里执行Python脚本，可以通过这里安装一些插件。


### Packages, Plugins, Resources and Other Terms

几乎Sublime Text的所有功能都可以扩展和自定义，可以修改编辑器行为，添加macro和snippets，扩展菜单等等。也可以利用编辑器API创建复杂插件。

Sublime Text的灵活性导致你需要学习很多配置文件。这些配置文件都是JSON活着XML格式。

本文中有时候讲这些配置文件叫做resources。

Sublime Text会查看packages folder下的resources。

package是指包含相关资源的目录。


### vi/vim Emlation

可以使用[Vintageous][3]让Sublime Text支持vi/vim

[3]: http://guillermooo.bitbucket.org/Vintageous



## 编辑

### 多字段选择修改

Sublime Text支持多处文本的同时修改：

- 选中需要修改的文字，编辑器会自动提示文档内的相同文本。
- 按`Cmd + d`添加下一个文本段到标记列表，如果需要跳过当前文本段，按`Cmd + k`然后`Cmd + d`
- 快捷键`Cmd + u`可以将当前文本段从编辑列表去掉
- `ESC`键可以退出编辑状态

### 整行选取

- `Cmd + l`可以选取光标所在行，活着当前选区所在所有行
- `Cmd + Shift + l`可以将选区的分裂为多选区，可同时编辑

### 文本选择

- `alt + shift + <arrow>`在对应放上增加下一个token入选区
- `ctrl + shift + m`选中光标所在括号的所有内容
- `Cmd + shift + j`选中与光标所在行相同缩进的内容

### 字符交换

- `ctrl + t`交换相邻的两个字母

## 搜索替换

Sublime Text支持Perl Compatible Regular Expressions (PCRE) engine的正则表达式搜索，默认搜索为普通搜索，需要在搜索框左边点击对应按钮切换到正则表达式搜索，也可以使用快捷键进行切换。

- `option + Cmd + r` 搜索的正则表达式和普通文本切换

### 单文件内的搜索和替换

- `Cmd ＋ f`打开搜索框
- `ESC`关闭搜索框
- `option + Cmd + c`切换区分大小写
- `option + Cmd + w`切换是否完整匹配：time 匹配timeOut或者 time单独单词
- `option + Enter`选中全部匹配结果并可同时编辑
- `Cmd + g`或者`Enter`查找下一个
- `Cmd + shift + g`查找前一个
- `Cmd + e`选中文本后按此快捷键可直接搜索

### 替换

- `option + Cmd + f`打开替换面板
- `Cmd + g`或者`Enter`查找下一个
- `option + Cmd + e`替换并查找下一个
- `option + Enter`当焦点在替换面版时会选中全部匹配项并处于可编辑状态

## 多文件搜索

- `Cmd + shift + f`打开多文件搜索面版

### 设置多文件搜索范围

在多文件搜索面版的Where栏指定搜索范围，支持以下方式的搜索范围指定：

- unix格式的路径
- 使用通配符排除特定文件
- symbolic location入：`<open folders>`,`<open files>`等

也可以使用逗号结合以上三种格式的范围。

## 文件导航和文件管理

### goto anything文件导航

搜索项目任意文件并导航。可在搜索栏进行过滤，可预览当前选中文件。

- `Cmd + p`打开文件面版
- `Enter`打开当前项并关闭导航面版
- `->`打开当前项，不关闭导航面版
- `ESC`关闭导航面版

更多操作：

- `Cmd + r`搜索当前文件内的[symbol][4]，如函数，类，或者markdown的标题
- `ctrl + g`跳转到指定行


### 左侧side bar导航

- `Cmd + k`然后`Cmd + b`可切换导航关闭状态

## 项目

Sublime Text将当前打开文件，文件夹当作一个project，将文件夹拖拽到side bar可添加到当前项目。菜单中选择**(Project -> Save Project As...)**可将相关文件文件夹关联信息保存，下次通过菜单**(Project -> open project)**可快速打开所有相关文件。


## Emmet支持react

[https://gist.github.com/wesbos/2bb4a6998635df97c748]()

## 使用[snippet][5]添加常用模板

将常用的代码段保存起来, 使用tab在设置好的文本上扩展出来,能提高效率,设置方法:

1. 菜单: Tools --> New Snippet... 自动打开模板
2. 编辑好之后保存在`Packages/User`目录下即可,文件后缀为`.sublime-snippet`

```
<snippet>
  <content><![CDATA[
Hello, ${1:this} is a ${2:snippet}.
]]></content>
  <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
  <!-- <tabTrigger>hello</tabTrigger> -->
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <!-- <scope>source.python</scope> -->
</snippet>
```

1. content下CDATA包裹的是扩展后的最终文本
2. tabTrigger是snippet的标识, 在标识后面按tab会用content替换标识
3. 触发替换的作用域

例子如下:

```
<snippet>
  <content><![CDATA[
@author: 德淸 deqing.qdq@alibaba-inc.com
]]></content>
  <tabTrigger>author</tabTrigger>
</snippet>
```

在author之后按tab键会扩展为`@author: 德淸 deqing.qdq@alibaba-inc.com`


[4]: http://docs.sublimetext.info/en/latest/reference/symbols.html
[5]: http://sublime-text-unofficial-documentation.readthedocs.org/en/latest/extensibility/snippets.html


# sublime text3基本使用及常用插件介绍

sublime text3下载地址：[http://www.sublimetext.com/3](http://www.sublimetext.com/3)本文所有操作都是基于Sublime Text3

这里是一个[非常全面的教程](http://zh.lucida.me/blog/sublime-text-complete-guide/)

## 快捷键列表

-   Ctrl + g    跳转到相应的行
-   Ctrl + m    在括号起始位置和终止位置之间切换
-   Ctrl + Shift + m    选中括号内内容
-   Ctrl + Shift + k    删除光标所在行
-   Ctrl + x    当光标选中区间时剪切选中区间，否则剪切光标所在行
-   Ctrl + Shift + up   向上选择行，并支持同时编辑多行
-   Ctrl + Shift + down 向下选择行，并支持同时编辑多行
-   Ctrl + l    选择光标所在行

## FAQs

### 不支持gbk编码

安装插件**ConvertToUTF8**,可能需要根据提示安装辅助插件

### HTML标签中间的大括号如何自动补全

菜单-> preferences -> Key Bindings - User在json配置文件中添加如下配置

    { "keys": ["{"], "command": "insert_snippet", "args": {"contents": "{$0}"}}

### 中文输入法不跟随输入位置

答：官方修复之前使用：IMESupport插件

### 如何为特定文件类型制定语法高亮，如为.handlebar文件设置html高亮

答：菜单中选择：`View > Syntax > Open all current extension as... > html`这样就可以为自定义后缀名文件使用所需的语法高亮



## Package Control插件管理

[Package Control][]是Sublime Text的包管理器，可以通过它安装[2000多个package][]。安装后的package将自动更新。基本上大部分工具通过自动和手动都可以完成安装。

### 通过控制台安装Package Control

1. 按快捷键ctrl + `调出控制台
2. 在控制台中运行如下代码

    ```
    import urllib.request,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
    ```

3. 以上代码将创建Package安装目录。并且下载[Package Control.sublime-package][]文件到目录下。
4. 重启Sublime Text，完成安装

### 手动安装Package Control
自动安装的原理其实就是在特定目录为Package Control创建文件夹和配置文件，手动创建所需目录，文件同样可以达到安装的目的：

1. 菜单中选择：`Preferences > Browse Packages...`
2. 在打开的资源管理器中向上一个目录，然后进入到`Installed Packages/`目录
3. 下载[Package Control.sublime-package][]并复制到`Installed Packages/`目录下
4. 重启Sublime Text，完成安装

[Package Control]: https://sublime.wbond.net/installation
[2000多个package]: https://sublime.wbond.net/browse
[Package Control.sublime-package]: https://sublime.wbond.net/Package%20Control.sublime-package


### 卸载已安装的插件

如果是通过package control安装的, 快捷键`Cmd + shift + p`打开面板搜索`Package Control: Remove Package`然后选择需要卸载的插件

## SidebarEnhancements

为左侧sidebar增加功能

## Emmet

[Emmet][]通过简洁的语法描述html内容，提高工作效率

### 使用Package Control安装Emmet

1. 快捷键`ctrl + shift + p`然后在控制窗口中输入`Package Control: Install Package`
2. 选择`Emmet`安装，重启Sublime Text完成安装

### 快捷键

#### Tab

在HTML, XML, HAML, CSS, SASS/SCSS, LESS and strings in programming languages (like JavaScript, Python, Ruby etc.)中按Emmet语法写好语句后`Tab`键即可生成所需的代码。

由于某些语言中Sublime Text默认的Tab行为是作者更期望的，可以在`Emmet.sublime-setting`文件中设置`disable_tab_abbreviations_for_scopes`来取消Tab在这些文件类型中的触发。具体方法见官网[tab-key-handler][]

### ctrl + e

默认在大部分自定义后缀名的文件中使用Tab是不能触发Emmet的，但是使用`ctrl + e`可以在任意文档中生效，这在编写html模板时非常有用

### Emmet基本语法

[emmet-zen-coding-tutorial][]是个很不错的教程，下面是一些简单的语法规则

#### 元素

通过元素名生成HTML标签，如div生成&lt;div>&lt;/div>，当需要生成自定义标签时，使用`ctrl + e`即可，如foo生成&lt;foo>&lt;/foo>

### 子元素嵌套&gt;

类似CSS子选择器`div>ul>li`生成

    <div>
        <ul>
            <li></li>
        </ul>
    </div>

### 兄弟元素+

类似CSS兄弟选择器，+生成兄弟关系的元素`div+p+div`生成

    <div></div>
    <p></p>
    <div></div>

### 向上操作符^

Emmet操作符的作用对象是基于当前上下文的，&gt;操作符会让上下文向下转移到深层元素，^操作符可以向上移动上下文，如`div+div>p>span+em`生成

    <div></div>
    <div>
        <p><span></span><em></em></p>
    </div>

通过^操作符修改上下文控制元素`div+div>p>span+em^bq`生成

    <div></div>
    <div>
        <p><span></span><em></em></p>
        <blockquote></blockquote>
    </div>

### 多元素操作符*

使用*后跟数字，生成对于数量的元素`ul>li*5`生成

    <ul>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
    </ul>

### 分组操作符()

使用括号分组完成复杂的逻辑`div>(div>ul>li*2>a)*2+footer>p`生成

    <div>
        <div>
            <ul>
                <li><a href=""></a></li>
                <li><a href=""></a></li>
            </ul>
        </div>
        <div>
            <ul>
                <li><a href=""></a></li>
                <li><a href=""></a></li>
            </ul>
        </div>
        <footer>
            <p></p>
        </footer>
    </div>

### id和class生成

使用类似css中id和class的语法，可以为元素添加所需属性如`div#header+div.cls1.cls2`生成

    <div id="header"></div>
    <div class="cls1 cls2"></div>

### 自定义属性

使用类似css中[attr]的语法添加自定义属性`td[title="Hello" colspan=3]`生成

    <td title="hello" colspan="3"></td>

### 元素编号$

使用*生成多个元素的时候，可以使用$进行编号`ul>li.item$*5`生成

    <ul>
        <li class="item1"></li>
        <li class="item2"></li>
        <li class="item3"></li>
        <li class="item4"></li>
        <li class="item5"></li>
    </ul>

### {}添加文本

`ul>(li{item $})*3`生成

    <ul>
        <li>item 1</li>
        <li>item 2</li>
        <li>item 3</li>
    </ul>

[Emmet]: https://github.com/sergeche/emmet-sublime
[tab-key-handler]: https://github.com/sergeche/emmet-sublime#tab-key-handler
[emmet-zen-coding-tutorial]: http://www.landfancy.com/archives/emmet-zen-coding-tutorial/

## Sublime Text Markdown Preview

[sublimetext-markdown-preview][]是Sublime Text的一个Markdown预览插件，有了它就可以轻松使用Sublime Text编辑Markdown文档了。

### 使用Package Control安装

1. 如果没有安装过Package Control，先安装
2. 按快捷键`Ctrl + shift + p`打开命令窗口
3. 在命令窗口中执行`Package Control: Install Package`
4. 选择`Markdown Preview`并进行安装

### 手动安装

1. 在菜单中选择`Preferences > Browse Packages...`
2. 在弹出的资源管理器中向上一个目录，然后进入到`Installed Packages/`目录
3. 下载[sublimetext-markdown-preview压缩包][]到`Installed Packages/`目录下并重命名为`Markdown Preview.sublime-package`
4. 重启Sublime Text

### 预览Markdown文件

1. 打开Markdown文件
2. 快捷键`Ctrl + shift + p`，在控制窗口中输入`Markdown Preview`
3. 此时有多个选项可选，一般选择`Markdown Preview: Preview in Browser`
4. 此时要求选择解析器，任选一个即可
5. Sublime Text在默认浏览器中打开Markdown解析后的html文件，有时候只是在新选项卡中打开了html文件，可以右键：`copy file path`然后到浏览器中访问即可

[sublimetext-markdown-preview]: https://github.com/revolunet/sublimetext-markdown-preview
[sublimetext-markdown-preview压缩包]: https://github.com/revolunet/sublimetext-markdown-preview/archive/master.zip


## sublime-autoprefixer

[sublime-autoprefixer][]根据[Can I Use][]数据库信息为CSS样式添加适当的厂商前缀，也可以自定义需要添加前缀的浏览器版本。

sublime-autoprefixer只对CSS起作用，不处理Sass或者LESS之类的预处理格式。

### 使用Package Control安装sublime-autoprefixer

1. 快捷键`ctrl + shift + p`然后控制台输入`Package Control: Install Package`
2. 在弹出窗口中输入`Autoprefixer`，安装，重启Sublime Text

### 使用autoprefixer

1. 支持整个css文件添加前缀，也可选中部分cas代码添加前缀
2. 快捷键`ctrl + shift + p`，输入`Autoprefix CSS`回车

[sublime-autoprefixer]: https://github.com/sindresorhus/sublime-autoprefixer
[Can I Use]: http://caniuse.com/

## 使用[BracketHighlighter][]高亮括号配对

高亮括号配对在查找时很方便

### 使用Package Control安装BracketHighlighter

1. 如果没有Package Control，先安装
2. 快捷键`ctrl + shift + p`，在控制窗口中输入`Package Control: Install Package`
3. 在控制窗口中输入`BracketHighlighter`并选择安装
4. 安装完成

## [auto-save][1]自动保存修改

这个插件在1秒没有按键时会自动保存。个人感觉太频繁，可根据需求选择

也可以启动sublime text的自动保存功能：

菜单： Sublime Text -> Preferences -> Settings - user

在配置文件中加上： `"save_on_focus_lost": true` 这样当前文档失去焦点时会自动保存

## [quoteHTML][02]将HTML拼接为js字符串



[02]: https://packagecontrol.io/packages/QuoteHTML
[01]: https://gist.github.com/eteanga/1736542
[1]:https://packagecontrol.io/packages/auto-save

[BracketHighlighter]: https://github.com/facelessuser/BracketHighlighter/tree/ST3
