

# chrome插件是什么

chrome插件本质是提供特殊功能的web页面，可以使用浏览器提供给web页面的所有功能，也能使用浏览器专属的API。前端工程师只需要了解chrome插件的组成、不同模块之间的通信方式、常见API的使用就可以轻松实现各种工具。

本次分享主要介绍chrome插件的组成、不同模块之间的通信，再完成一个小插件。

# helloworld： 淘宝账号退出

1. 创建项目目录`logout`
2. 新建`manifest.json`

        {
        "manifest_version": 2,

        "name": "淘宝退出",
        "description": "添加淘宝退出按钮到浏览器窗口，方便测试",
        "version": "1.0",

        "browser_action": {
            "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
            }
        },

        "background": {
            "scripts": ["js/logout.js"],
            "persistent": false
        },

        "permissions": ["<all_urls>"]
        }

3. 创建`js/logout.js`

        chrome.browserAction.onClicked.addListener(function () {
        var xhr = new XMLHttpRequest();
        xhr.open('get', 'http://login.taobao.com/member/logout.jhtml');
        xhr.send(null);
        });

4. 创建项目所需的png文件

5. 浏览器访问：`chrome://extensions/` -》 勾选**开发者模式** -》加载已解压的扩展程序 -》选择logout文件夹
6. 浏览器地址栏右侧出现icon，点击之后淘宝账号退出。
7. 选择打包扩展程序，选择logout目录。完成打包获得crx文件



# 插件文件组成

- 必选的`manifest.json`: 插件功能, 权限, 基本信息的配置, 前往[Manifest File Format][5]查看详细介绍
- 可选的一个或多个HTML文件
- 可选的一个或多个JavaScript 文件
- 可选的其他资源: 如图片


# chrome插件UI类型

chrome插件可以通过以下UI与用户进行交互:

- [browser actions][1]: 出现在地址栏右边工具栏的icon, 包含可选的icon, tooltip, badge, popup, 在所有页面都会展示, 通常用来实现大部分页面需要的功能.

    下图是一个带icon和popup的browser action, 点击icon弹出popup
    ![][2]
- [page actions][3]: 出现在地址栏内靠右的icon, 包含可选的icon, tooltip, popup, 只展现在少数页面, 通常用来实现特殊页面所需的功能. 一个插件不能同时拥有page action和browser action

    ![][4]

- [Desktop Notifications][6]: 浏览器窗口弹窗提示, 见demo: notifications
- [Omnibox][7]: 地址栏输入注册的关键字之后触发交互, 见demo: omnibox/simple-example
- [Options Pages][8]: 允许用户配置插件
- [Theme][9]: 一种特殊的插件, 修改浏览器外观, 不包含js, HTML,
- [dev tool][19]: 自定义&扩展 dev tool, 见demo: devtools/panels/chrome-query，可以使用DevTools相关的api：
    - [devtools.inspectedWindow][25]
    - [devtools.network][26]
    - [devtools.panels][27]

# 插件架构

根据功能和上下文将页面分为一下几类

- **UI pages**: 与用户交互的普通页面, 不同页面之间可以互相操作(后续会介绍)， 常见的UI page有：
    - browser action的popup页面
    - 插件的options pages
    - override page
    - 也可以用[tabs.create][12]或者`window.open()`打开插件中的html文件

    ![][14]


- **background page**: 特殊的UI page, 插件状态和主要业务逻辑, 普通页面向background page请求插件状态
    - [Event Pages][10]: 需要用到的时候才创建, 不需要的时候自动销毁, 释放资源, **推荐**
    - [persistent pages][11]: 插件开启期间始终存在

- **content script**: 用于操作当前加载的页面, 不能直接操作插件的其他页面. content script只能通过发送消息和插件进行交互(后续会介绍).

    - 只能使用少数`chrome.*`API
    - 不能使用插件页面定义的变量和函数
    - 不能使用其他content script定义的变量和函数
    - 可以使用跨域Ajax

    ![][13]

- **DevTools page**: DevTool窗口打开的时候会创建DevTools page，窗口关闭时销毁。它和content script一样只能使用少数API，可以使用DevTool的特殊API。和content script一样使用消息发送与background page通信。

    ![][28]

# UI页面之间的通信

- 插件内部的页面之间可以直接访问对方的方法和DOM, 使用[chrome.extension.getViews()][15]获取所有页面window的数组，[chrome.extension.getBackgroundPage()][16]获取background page的window
    
    获取目标页面的window之后就可以直接访问对应page进行操**不推荐**

- 和content script一样使用消息发送



# content scrip和UI页面间的通信

参考[Message Passing][20]: content script和UI页面通过消息发送进行通信。chrome支持以下几种通信方式：

- 简单的一次性消息发送：可以使用[runtime.sendMessage()][21]或者[tabs.sendMessage()][22]在content script和插件之间发送消息，同时支持回调

    从content script发送一个请求：

        chrome.runtime.sendMessage({
            type: 'GET_INTERVAL'
        }, function (newInterval) {
            interval = newInterval
            console.log(interval)
        })

    从插件向content script发送请求：

		chrome.tabs.query({currentWindow: true}, function (tabs) {
			if (tabs) {
				tabs.forEach(tab => {
					chrome.tabs.sendMessage(tab.id, {
						type: 'STOP_COUNTDOWN'
					})
				})
			}
		})

    插件和content script监听事件的方式都一样：

        chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
            var origin = sender.tab ? 'content script' : 'extension'
            console.log(`content script收到来自${origin}的消息:${request.type}`)

            if (request.type === 'STOP_COUNTDOWN') {
                isRunning = false
            }
        })
    

- 高级发送： 参考[Long-lived connections][23]
- 如果知道其他插件的id， 还可以发送[跨插件消息][24]

# 数据存储

插件可以使用[chrome.storage][17]或者[web storage API][18]将数据保存到本地, 也可以发送到服务器保存.





# 参考资料

- 官方demo: [https://chromium.googlesource.com/chromium/src/+/master/chrome/common/extensions/docs/examples/api]()


[28]: https://developer.chrome.com/static/images/devtools-extension.png
[27]: https://developer.chrome.com/extensions/devtools.panels
[26]: https://developer.chrome.com/extensions/devtools.network
[25]: https://developer.chrome.com/extensions/devtools.inspectedWindow
[24]: https://developer.chrome.com/extensions/messaging#external
[23]: https://developer.chrome.com/extensions/messaging#connect
[22]: https://developer.chrome.com/extensions/tabs#method-sendMessage
[21]: https://developer.chrome.com/extensions/runtime#method-sendMessage
[20]: https://developer.chrome.com/extensions/messaging
[19]: https://developer.chrome.com/extensions/devtools
[18]: http://dev.w3.org/html5/webstorage/
[17]: https://developer.chrome.com/extensions/storage
[16]: https://developer.chrome.com/extensions/extension#method-getBackgroundPage
[15]: https://developer.chrome.com/extensions/extension#method-getViews
[14]: https://developer.chrome.com/static/images/overview/arch-2.gif
[13]: https://developer.chrome.com/static/images/overview/arch-cs.gif
[12]: https://developer.chrome.com/extensions/tabs#method-create
[11]: https://developer.chrome.com/extensions/background_pages
[10]: https://developer.chrome.com/extensions/event_pages
[9]: https://developer.chrome.com/extensions/themes
[8]: https://developer.chrome.com/extensions/optionsV2
[7]: https://developer.chrome.com/extensions/omnibox
[6]: https://developer.chrome.com/extensions/desktop_notifications
[5]: https://developer.chrome.com/extensions/manifest
[4]: https://developer.chrome.com/static/images/overview/mappy.png
[3]: https://developer.chrome.com/extensions/pageAction
[2]: https://developer.chrome.com/static/images/browser-action.png
[1]: https://developer.chrome.com/extensions/browserAction