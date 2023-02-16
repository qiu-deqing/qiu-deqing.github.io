---
title: 微信小程序开发
---

# 开发者工具

[开发者工具][1]，集成了开发调试、代码编辑及程序发布等功能。主要有三大功能区：模拟器、调试工具和小程序操作区

1. 下载并安装开发者工具

    到[开发者工具下载页面][2]下载所需的安装开发者工具, 目前有windows64, windows32, mac三种版本.

    **发布计划:**内测阶段，开发工具将会稳定的按照 2 周一次的节奏发布版本更新，但如果遇到影响的使用的问题，小程序团队会在 2 个版本之间发布小版本来解决这些问题。


2. 安装完成后扫码登录

    启动工具时，开发者需要使用已在后台绑定成功的微信号扫描二维码登录，**后续所有的操作都会基于这个微信帐号**

    ![][3]

# 开发流程

## 1. 获取微信小程序AppID

如果是收到邀请的开发者. 会有一个AppID, 没有内测账号只是不能上传，但完全可以在本地开发和测试。

## 2. 创建项目

安装好开发者工具后, 使用微信扫码登录. 选择**添加项目**.填写以下信息

![][4]

- AppID: 选择无AppID
- 填写项目名称: 如helloworld
- 项目目录: 新建好一个空文件夹用于保存项目
- 勾选在当前目录创建quick start: 开发者工具会自动添加脚手架代码

点击添加项目之后会创建demo项目并打开项目:

![][5]

左侧导航主要功能:

- 编辑: 代码编辑
- 调试: 本地运行, 调试app
- 项目: 发送项目到手机预览效果

# 程序组成

小程序包含一个描述整体程序的app和多个描述各自页面的page.

app由三部分组成,必须放在项目根目录, 文件和功能如下:

|       文件      |      必须      |      作用     |
|-----------------|---------------|---------------|
| [app.js][6]      |     是       |     小程序逻辑   |
| [app.json][7]    |     是       | 小程序公共设置    |
| [app.wxss][8]    |     否        | 小程序公共样式表  |


一个小程序页面由四个文件组成:

|    文件类型    |    必填      |    作用     |
|---------------|-------------|--------------|
|     [js][9]   |     是       |   页面逻辑   |
|    [wxml][10]  |    是       |   页面结构   |
|    [wxss][11]  |    否       |   页面样式   |
|    [json][12]  |    否        |  页面配置    |

**同一个页面的四个文件必须具有相同的路径和文件名**

# app.json: 小程序全局配置

app.json配置页面文件的路径、窗口表现、网络超时时间、多tab等。

如以下`app.json`：

    {
      "pages": [
        "pages/index/index",
        "pages/logs/index"
      ],
      "window": {
        "navigationBarTitleText": "Demo"
      },
      "tabBar": {
        "list": [{
          "pagePath": "pages/index/index",
          "text": "首页"
        }, {
          "pagePath": "pages/logs/logs",
          "text": "日志"
        }]
      },
      "networkTimeout": {
        "request": 10000,
        "downloadFile": 10000
      },
      "debug": true
    }

app.json配置项列表

|       属性       |         类型    |    必填    |     功能    |
|------------------|-----------------|-----------|-------------|
|    [pages][13]  |  String Array   |      是     |   设置页面路径  |
|    [window][14] |  Object          |     否     | 设置默认页面的窗口表现   |
|    [tabBar][15]  |  Object         |      否    |   设置底部tab表现    |
|    [networkTimeout][16] | Object   |     否    |   设置网络超时时间    |
|    [debug][17]    |  boolean       |    否     |  设置是否开启debug模式  |

# 逻辑层（App Service）

小程序逻辑层由JavaScript编写，逻辑层将数据处理后发送给视图层、接收视图层事件反馈。小程序JavaScript运行时环境：

- 具有[App][16]和[Page][17]方法，用于程序和页面的注册。
- `getApp()`获取App实例，`getCurrentPages()`获取当前页面栈
- 丰富[API][18]，提供微信特有功能如：获取微信用户数据，扫一扫，支付
- 每个页面由独立的[作用域][19]， 并提供[模块化][20]能力

# 基础组件

组件类似HTML标签， **所有组件与熟悉都是小写，以`-`链接**。

### 属性类型

|     类型    |      描述        |     备注                      |
|  Boolean     |  布尔值         | 组件写上该属性，不管该属性等于什么，值都为`true`，没写该属性是，属性值才为`false`,如果属性值为变量，变量的值会被转换为Boolean类型|
|  Number    | 数字              |  `1`, `2.5`   |
|  String    |  字符串           |  `"hello"`      |
|   Array    |  数组             | `[1, "hello"]`  |
|  Object     | 对象             |  `{ key: value }` |
| EventHandler | 事件处理函数     | ``


# WXSS(WeiXin Style Sheets)

WXSS具有CSS大部分特性同时对CSS进行了扩充、修改。

## 扩展特性

扩展特性有：

- 尺寸单位
- 样式导入

### 尺寸单位

- **rpx(responsive pixle)**：可以根据屏幕宽度进行自适应。规定屏幕宽为750rpx。如在 iPhone6 上，屏幕宽度为375px，共有750个物理像素，则750rpx = 375px = 750物理像素，1rpx = 0.5px = 1物理像素。

    |           设备        |      rpx换算px（屏幕宽度/750)     |    px换算rpx(750/屏幕宽度)    |
    |-----------------------|---------------------------------|------------------------------|
    |    iPhone5            |  1rpx = 0.42px                  |   1px = 2.34rpx               |
    |    iPhone6            | 1rpx = 0.5px                     |   1px = 2rpx                 |
    |    iPhone6 Plus       | 1rpx = 0.552px                   | 1px = 1.81rpx                |

    疑问： rpx使用场景是什么

- **rem（root em）**：规定屏幕宽度为20rem**（不是10rem）**： 1rem = (750/20)rpx


### 样式导入

使用`@import`可以导入外联样式表， `@import`后跟需要导入的外联样式表的**相对路径**， 已`;`结束语句。

疑问： 类似复制粘贴吗？用法：部分页面使用的主题？

示例：

    /** common.wxss **/
    .small-p {
        padding: 5px;
    }


    /** app.wxss **/
    @import "common.wxss";
    .middle-p {
        padding: 15px;
    }

## 内联样式

通过`style`, `class`为元素设置样式： 和CSS一样。

- **style**： 推荐style中只设置动态样式，静态样式写到外联样式表中。

        <view style="color: {{color}};"/>

- **class**: 指定外联样式表中的类。

        <view class="normal"/>

## 选择器

目前支持的选择器：

|          选择器        |       样例      |       样例描述        |
|        .class         |    `.intro`     |  选择所有class包含`intro`的组件  |
|        #id             |  `#firstname`    |  选择id="firstname"的组件   |
|       element         |   `view`         |   选择所有view组件         |
|   element, element     | `view, checkbox` | 选择文档所有的view组件和checkbox组件 |
|   ::after             |  `view:after`      | view组件后插入内容      |
|   ::before            |  `view::before`     | view组件钱插入内容      |

疑问： element, element样例是否错了

## 全局样式与局部样式

- 定义在**app.wxss**中的为全局样式， 作用于**所有页面**
- 定义在page中的wxss为局部样式，作用于**对应页面**，且会覆盖**app.wxss**中相同选择器样式

# FAQs

1. Failed to load resource: net::ERR_NAME_NOT_RESOLVED http://1709827360.appservice.open.weixin.qq.com/appservice

    问题原因：通常是由于系统设置了代理如Shadowsocks等。
    解决办法: 关闭代理,取消代理设置

[19]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/app-service/module.html?t=1477656500928#文件作用域
[18]: https://mp.weixin.qq.com/debug/wxadoc/dev/api/?t=1477656500928
[17]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/app-service/page.html?t=1477656500928
[16]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/app-service/app.html?t=1477656500928
[15]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html?t=1477656487816#tabbar
[14]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html?t=1477656487816#window
[13]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html?t=1477656487816#pages
[12]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html?t=1477656487816#pagejson
[11]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxss.html?t=1477656487816
[10]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxml/?t=1477656487816
[9]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/app-service/page.html?t=1477656487816
[8]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxss.html?t=1477656487816
[7]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html?t=1477656487816
[6]: https://mp.weixin.qq.com/debug/wxadoc/dev/framework/app-service/app.html?t=1477656487816
[5]: https://gw.alicdn.com/tps/TB1DLQkNVXXXXaCXVXXXXXXXXXX-1405-877.png
[4]: https://gw.alicdn.com/tps/TB1zOIzNVXXXXaaXFXXXXXXXXXX-600-502.png
[3]: https://gw.alicdn.com/tps/TB1PnUdNVXXXXbWaXXXXXXXXXXX-600-502.png
[2]: https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/download.html?t=1476197490605
[1]: https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/devtools.html?t=1476197480996
