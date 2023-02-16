---
title: 无线端web开发学习
---



谷歌搜索: **mobile web development**


[Fundamental of Mobile Web Development][1]: 一系列web开发资源

## [fundamentals/getting-started/your-first-progressive-web-app][2]

[Progress Web App][3]特点:

- Progresssive: 适合所有用户, 核心思想是渐进增强功能
- Responsive: 适配所有设备: PC, 手机, 平板
- Connectivity independent: 在慢速或者离线环境下使用**service workers**增强体验
- App-like: 建立在**app shell model**上以提供app一样的交互, 导航
- safe: HTTPS防止泄密
- Discoverable: W3C manifests和`service worker registration scope`使应用可以被搜索引擎发现并且识别为app
- re-engageable: 消息推送等功能使得re-engagement变得容易
- installable: 允许用户将快捷方式保存到桌面(不需要通过app store下载)
- Linkable: 通过URL就可以分享, 不需要安装

本教程使用Progressive Web App技术创建一个天气预报app

## 1. [Architect the App Shell][4]

app的内核指app所需的最核心的HTML, CSS和JavaScript, 它是确保app高性能的起点. 它加载起来非常快并且马上被缓存, 不需要每次都加载,
只需要在使用的时候按需加载内容.

App内核架构将app核心, UI与数据分离开. 所有的UI和基础资源都讲通过service worker缓存到本地, app后续使用只需要获取相关数据即可.

App内核架构模式让你更好的关注性能, 瞬时加载, 方便更新

## 设计app内核

首先需要将系统拆分为核心组件, 需要考虑一下问题:

1. 什么需要立刻展现在屏幕上
2. 哪些是app的关键UI组件
3. app内核需要哪些资源(图片, js, css等)

我们创建天气预报app, 核心组件包含以下内容:

- 包含标题, 增加, 刷新按钮的头部
- 预报卡片容器
- 预报卡片模板
- 增加城市的对话框
- 加载指示

在设计复杂app的时候, 不是初始化所必须的内容可以稍后请求然后缓存, 为后续使用加速. 比如我们可以将新建城市对话框延迟到App启动之后并且有空余时间的时候加载.

## 实现app内核

推荐使用Web Starter Kit作为项目起点, 但是我们的项目将尽可能简单以几种在Progressive Web App本身.

可以[下载本教程app完整代码][5]获取项目所必须的资源文件.

- 创建App内核所需的HTML

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="initial-scale=1.0, width=device-width, user-scalable=no">
    <title>Weather App</title>
</head>
<body>

<header class="header">
    <h1 class="header__title">Weather App</h1>
    <button id="butRefresh" class="headerButton"></button>
    <button id="butAdd" class="headerButton"></button>
</header>

<main class="main" hidden></main>

<div class="dialog-container">
</div>

<div class="loader">
    <svg viewBox="0 0 32 32" width="32" height="32">
        <circle id="spinner" cx="16" cy="16" r="14" fill="none"></circle>
    </svg>
</div>

</body>
</html>
```

在上面的代码中`main`设置为`hidden`, 加载器设置为显示. 这样确保用户进入页面能清楚地知道内容正在加载.

新建城市对话框和预报卡片的模板在`resources`目录下, 把它们复制到对应的地方即可.

- 为核心UI组件添加样式


- 添加关键js代码

关键代码包含以下内容:

- `app`对象包含app所需的关键信息
- 给所有按钮监听事件
- 更新天气预报卡片的函数`app.updateForecastCard`
- 从Firebase Public Weather API获取最新天气预报数据的函数`app.getForecases`
- 循环卡片并调用`app.getForecast`以获取最新预报的函数`app.updateForecasts`
- 用于方便测试的假数据`fakeForecast`

## 快速加载

Progressive Web App应该快速启动并且马上可用. 后端在输出app内核的时候, 应该根据用户所在IP请求对应城市的天气信息,
包含在`initialWeatherForecast`中用于前端渲染, 这样用户就可以第一时间获取到真实数据

将用户偏好设置使用IndexedDB这样的技术存储起来, 教程中使用`localStorage`, 实际项目中应该尽量避免使用, 因为它属于阻塞, 同步存储操作, 在有的设备上可能很慢, 影响用户体验.

```
app.saveSelectedCities = function () {
var selectedCities = JSON.stringify(app.selectedCities)
localStorage.selectedCities = selectedCities
}
```

接下来添加启动代码以检查用户是否已经选择过城市, 并且渲染对应天气预报.

```
  app.selectedCities = localStorage.selectedCities
  if (app.selectedCities) {
    app.selectedCities = JSON.parse(app.selectedCities)
    app.selectedCities.forEach(function (city) {
      app.getForecast(city.key, city.label)
    })
  } else {
    app.updateForecastCard(initialWeatherForecast)
    app.selectedCities = [
      {key: initialWeatherForecast.key, label: initialWeatherForecast.label}
    ]
    app.saveSelectedCities()
  }
```

## 使用Service worker对app内核进行Pre-cache

为了保证Progressive Web App快递, 可安装以实现在线, 离线, 低网速情况下可用. 我们需要使用service worker缓存app内核


总结:

**设计最小, 最快加载, 可缓存的app内核, 从服务器取数据渲染模板**



支付宝无线端校验总结[http://am-team.github.io/amg/dev-exp-doc.html][6]



[6]: http://am-team.github.io/amg/dev-exp-doc.html
[5]: https://developers.google.com/web/fundamentals/getting-started/your-first-progressive-web-app/pwa-weather.zip
[4]: https://developers.google.com/web/fundamentals/getting-started/your-first-progressive-web-app/step-01?hl=en
[3]: https://developers.google.com/web/progressive-web-apps
[2]: https://developers.google.com/web/fundamentals/getting-started/your-first-progressive-web-app/
[1]: https://developers.google.com/web/updates/2014/12/Fundamental-of-Mobile-Web-Development?hl=en
