---
title: 事件发射器(event emitter)
---

事件发射器是一种发布，订阅模式，对程序解耦有很大帮助。Node提供了[Event Emitter][1]的内置实现并且被广泛应用。浏览器端也有很多对应的实现，本文简要介绍它的基本使用。

## 注册监听器

我们假设有一个EventEmitter对象job，为`done`事件注册监听器可以这样实现：

```
job.on('done', function () {
  console.log('The job is done!');
});
```

## 事件发射

当任务完成时需要告诉监听器开始执行，调用`emit`方法即可：

```
job.emit('done');
```

同时也可以向监听器传递参数：

```
var timeDone = new Date();
job.emit('done', timeDone);
```

参数会按照相同顺序传递给监听器：

```
job.on('done', function (timeDone) {
  console.log('Job was pronounced done at: ', timeDone);
});
```

## 移除监听器

调用`off`方法可以移除监听器：

```
function onDone(timeDone) {
  console.log('Job was pronounced done at: ', timeDone);
}
job.on('done', onDone);

job.off('done', onDone);
```

也可以使用`removeAllListeners`移除所有监听器

```
job.removeAllListeners();
```

## 让事件只执行一次

使用`once`设置监听器可以让监听器之调用一次

```
job.once('done', function () {
  // this callback will only be called the
  // first time `done` is fired
});
```

## 创建自己的EventEmitter

通常情况下需要根据业务逻辑创建自己的EventEmitter，使用继承可以很好地达到目的：

```
function Job() {
  EventEmitter.call(this);
  // custom initialization here
}

// 最简单的继承，可以改进
Job.prototype = new EventEmitter;
```

上面是不使用任何工具继承EventEmitter的最简单方法。`EventEmitter.call(this)`也不是必须的，因为EventEmitter的构造函数不执行任何操作，添加这句代码时为了防止将来它执行什么操作。

以下是一些常见的继承方法。

- 使用[Node's util.inherits][2]或者[component/inherit][3]这样的继承函数：

    ```
    inherit(Job, EventEmitter);
    ```

- 使用[__proto__][4]:

    ```
    Job.prototype.__proto__ = EventEmitter.prototype;
    ```

- 使用[xtend][5]或者[_.extend][6]的mixin代替继承

    ```
    extend(Job.prototype, EventEmitter.prototype);
    ```

不管用了什么方法实现继承，都可以使用`new`直接新建对象了：

```
var job = new Job();
```

现在job就成为了一个真正的event emitter。

## 常见Event Emitter工具库实现

可以采用现有的成熟框架，通过继承即可食用：

- Component: [component/emitter][7]
- Bower or standalone: [Wolfy87/EventEmitter][8]


## 亲自实现一个简单的EventEmitter

EventEmitter实现并不难，我们可以实现一个简单的版本，加深理解。具备以下基本功能：

- `on`: 为特定事件添加监听器
- `off`: 为特定事件移除监听器
- `emit`: 触发特定事件
- `once`: 注册只执行一次的监听器

```
var EventEmitter = (function () {

  function EventEmitter() {}

  var proto = EventEmitter.prototype;

  proto.on = function (type, handler) {
    this._callbacks = this._callbacks || {};
    this._callbacks[type] = this._callbacks[type]|| [];
    this._callbacks[type].push(handler);
    return this;
  };

  proto.off = function (type, handler) {
    var list = this._callbacks && this._callbacks[type];

    if (list) {
      var i = list.length;
      while (i--) {
        if (list[i] === handler) {
          list.splice(i, 1);
        }
      }
    }

    return this;
  };

  proto.once = function (type, handler) {
    var selft = this;

    function wrapper() {
      handler.apply(self arguments);
      self.off(type, wrap);
    }
    this.on(type, wrapper);
    return this;
  };

  proto.trigger = function (type, data) {
    var callbacks = this._callbacks && this._callbacks[type];
    if (callbacks) {
      callbacks.forEach(function (callback) {
        callback(data);
      });
    }
  };
}());
```

## 使用Event Emitter

[2048][9]使用了`KeyboardInputManager`类来管理用户事件，其基本思路就是Event Emitter，游戏操作支持键盘，触屏等交互，将不同的事件进行处理，然后发布具有业务逻辑含义的事件。这样可以有效分离业务逻辑层和UI层，实现解耦。以下是[keyboard_input_manager.js][10]实现：

```
// 监听用户交互，并转化为具有业务逻辑含义的事件
function KeyboardInputManager() {
  this.events = {};

  // 兼容性处理
  if (window.navigator.msPointerEnabled) {
    // Internet Explorer 10 style
    this.eventTouchstart = 'MSPointerDown';
    this.eventTouchmove = 'MSPointerMove';
    this.eventTouchend = 'MSPointerUp';
  }
  else {
    this.eventTouchstart = 'touchstart';
    this.eventTouchmove = 'touchmove';
    this.eventTouchend = 'touchend';
  }

  this.listen();
}

// EventEmitter.prototype.on的简单实现
KeyboardInputManager.prototype.on = function (event, callback) {
  if (!this.events[event]) {
  this.events[event] = [];
  }
  this.events[event].push(callback);
};

// EventEmitter.prototype.emit 的简单实现
KeyboardInputManager.prototype.emit = function (event, data) {
  var callbacks = this.events[event];
  if (callbacks) {
    callbacks.forEach(function (callback) {
      callback(data);
    });
  }
};

// 基本事件监听，然后转化为业务逻辑事件
KeyboardInputManager.prototype.listen = function () {
  var selft = this;

  // 支持多种按键
  var map = {
    38: 0, // Up
    39: 1, // Right
    40: 2, // Down
    37: 3, // Left
    75: 0, // Vim up
    76: 1, // Vim right
    74: 2, // Vim down
    72: 3, // Vim left
    87: 0, // W
    68: 1, // D
    83: 2, // S
    65: 3  // A
  };

  // 方向键
  document.addEventListener('keydown', function (event) {
    var modifiers = event.altKey || event.ctrlKey || event.metaKey ||
      event.shiftKey;

    // 工具键码映射到自定义方向码
    var mapped = map[event.which];

    if (!modifiers) {
      if (mapped !== undefined) {
        event.preventDefault();
        // 发送逻辑事件
        self.emit('move', mapped);
      }
    }

    // 如果是r，重新开始游戏
    if (!modifiers && event.which === 82) {
      // 封装的restart方法，用于发射`restart`事件
      self.restart.call(self, event);
    }
  }, false);


  // 监听页面按钮
  this.bindButtonPress('.retry-button', this.restart);
  this.bindButtonPress('.restart-button', this.restart);
  this.bindButtonPress('.keep-playing-button', this.keepPlaying);

  // 触屏滑动事件
  var touchStartClientX,
    touchStartClientY;
  var gameContainer = document.querySelector('.game-container');
  gameContainer.addEventListener(this.eventTouchstart, function (event) {
    if ((!window.navigator.msPointerEnabled && event.touches.length > 1) ||
        event.targetTouches > 1) {
      return;   // 忽略多个手指触摸
    }

    // 兼容性处理
    if (window.navigator.msPointerEnabled) {
      touchStartClientX = event.pageX;
      touchStartClientY = event.pageY;
    }
    else {
      touchStartClientX = event.touches[0].clientX;
      touchStartClientY = event.touches[0].clientY;
    }

    // 取消用户代理可能发送的鼠标事件
    event.preventDefault();
  }, false);

  gameContainer.addEventListener(this.eventTouchmove, function (event) {
    // 取消用户代理可能发送的鼠标事件，以及页面滑动
    event.preventDefault();
  }, false);

  gameContainer.addEventListener(this.eventTouchend, function (event) {
    if ((!window.navigator.msPointerEnabled && event.touches.length > 0) ||
        event.targetTouches > 0) {
      return; // 忽略多个触点
    }

    var touchEndClientX,
      touchEndClientY;

    if (window.navigator.msPointerEnabled) {
      touchEndClientX = event.pageX;
      touchEndClientY = event.pageY;
    }
    else {
      touchEndClientX = event.changedTouches[0].clientX;
      touchEndClientY = event.changedTouches[0].clientY;
    }

    var dx = touchEndClientX - touchStartClientX;
    var absDx = Math.abs(dx);

    var dy = touchEndClientY - touchStartClientY;
    var absDy = Math.abs(dy);

    if (Math.max(absDx, absDy) > 10) {
      // (right: left) : (down: up)
      self.emit('move', absDx > absDy ? (dx > 0 ? 1 : 3) : (dy > 0 ? 2 : 0));
    }
  }, false);


  KeyboardInputManager.prototype.restart = function (event) {
    event.preventDefault();
    this.emit('restart');
  };

  KeyboardInputManager.prototype.keepPlaying = function (event) {
    event.preventDefault();
    this.emit('keepPlaying');
  };

  KeyboardInputManager.prototype.bindButtonPress = function (selector, fn) {
    var button = document.querySelector(selector);
    button.addEventListener('click', fn.bind(this));
    button.addEventListener(this.eventTouchend, fn.bind(this));
  };
};
```

## 参考链接

- [https://github.com/gabrielecirulli/2048/blob/master/js/keyboard_input_manager.js][10]
- [https://github.com/gabrielecirulli/2048][9]
- [https://github.com/Wolfy87/EventEmitter][8]
- [https://github.com/component/emitter][7]
- [http://underscorejs.org/#extend][6]
- [https://github.com/Raynos/xtend][5]
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto][4]
- [https://github.com/component/inherit][3]
- [http://nodejs.org/api/util.html#util_util_inherits_constructor_superconstructor][2]
- [http://nodejs.org/api/events.html][1]

[10]: https://github.com/gabrielecirulli/2048/blob/master/js/keyboard_input_manager.js
[9]: https://github.com/gabrielecirulli/2048
[8]: https://github.com/Wolfy87/EventEmitter
[7]: https://github.com/component/emitter
[6]: http://underscorejs.org/#extend
[5]: https://github.com/Raynos/xtend
[4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto
[3]: https://github.com/component/inherit
[2]: http://nodejs.org/api/util.html#util_util_inherits_constructor_superconstructor
[1]: http://nodejs.org/api/events.html



