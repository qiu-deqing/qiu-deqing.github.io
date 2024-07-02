---
title: reliable javascript笔记
---

![][1]


# 2 Tooling up

- 使用Jasmine测试组件
- 使用依赖注入容器提高模块化, 可复用性, 可测试性
- 研究AOP(aspect-oriented programming)如何使代码简单可靠
- 研究测试驱动开发按哪里
- 使用JSLint检查代码

## 使用依赖注入框架


### 最简陋的依赖注入

```
Attendee = function (service, messenger, attendeeId) {
  if (!(this instanceof Attendee)) {
    return new Attendee(attendeeId);
  }
  this.attendeeId = attendeeId;
  this.service = service;
  this.messenger = messenger;
}

var attendee = new Attendee(new ConferenceWebSvc(), new Messenger(), id);
```

### 依赖注入注意事项

创建新对象时, 如果一下问题的任意一项答案为**Yes**, 应当使用注入而不是直接实例化

- 对象或者他的组件依赖于外部资源? 如数据库, 配置文件, HTTP请求
- 测试是否要对对象内部错误进行处理
- Will some of my tests want the object to behave in particular ways?
- Is the object one of my own, as opposed to one from a third-party library?


### 案例研究: 实现一个轻量级依赖注入框架


专业的依赖注入框架工作模式如下:

1. 当应用程序启动之后, 在框架容器内注册可注入的元素, 每个元素用名字区分
2. 当需要对象时, 从容器获取
3. 容器实例化你需要的对象, 实例化的过程中可能需要递归实例它所依赖的对象


我们的容器应该做两件事:

- 接收组件注册
- 用户请求组件时实例化并返回.

我的注册函数`register`需要三个参数

- 注册组件的名字
- 组件所依赖组件的名字
- 一个返回组件对象的工厂函数. 也就是说当你向组件请求组件时, 容器执行这个函数然后把它的返回值返回给用户. 容器应该首先获取组件的依赖组件并以参数形式传递给工厂函数.


**TDD**要求每一个阶段编写最少的代码, 所以我们以一个空函数开始. 因为这个函数应该被所有`DiContainer`实例共享, 我们把它放在原型上.

```
DiContainer = function () {};

DiContainer.prototype.register = function (name, depencencies, func) {};
```

我们为这个函数编写测试用例[DiContainer00-test.js][2], 需要注意:

- 每次测试前实例化单独的container, 保证测试之间不干扰
- 两个嵌套的describe文本与it文本结合组成可读句子

现在执行测试会失败, 我们需要给`register`函数添加类型检查DiContainer01.js

```
DiContainer.prototype.messages = {
  registerRequiresArgs: 'The register function requires three arguments: ' +
    'a string, an array of strings, and a function'
};
```

我们将错误提示信息包含在prototype中, 方便用户自定义修改, 维护.

下一步我们实现`get(name)`函数, 根据名字实例化对象, 仍然先写测试用例检查类型

[2]: https://github.com/qiu-deqing/reliable-javascript-code/blob/master/ch02-tooling-up/dependence-inject/DiContainer00-test.js
[1]: http://gtms01.alicdn.com/tps/i1/TB1XZpnKVXXXXcoXXXXvEcy2XXX-398-499.jpg
