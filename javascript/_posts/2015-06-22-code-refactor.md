---
title: 代码重构
---

不注意代码结构，代码难以理解，修改，维护，结合最近一个需求进行总结。

## 需求介绍

![][1]

假设上面是某微博账号开礼盒活动，打开礼盒的逻辑如下(假设用户已登陆)：

- 检查是否已关注账号
  + 如果用户已关注，请求开礼盒接口
  + 如果用户未关注，询问是否关注，如果用户选择关注，请求关注接口
    * 如果关注成功，请求开礼盒接口
    * 如果关注失败，显示失败原因
- 请求开礼盒会返回不同礼品，需根据不同礼品显示不同反馈


现有后端接口如下：

### 关注账号

url: `follow.json`

返回字段说明：

```
{
  info: {
    ok: true,
    code: '1',
    msg: 'message'
  },
  data: {}
}
```

可能返回的结果

|    code   |   msg    |  说明     |
|-----------|----------|----------|
|   1      |  sucess  | 关注成功   |
|   2      |  fail    | 关注失败   |

### 检查关注接口

url: `checkFollow.json`

返回字段说明：

```
{
  info: {
    ok: true,
    code: '1',
    msg: 'message'
  },
  data: {}
}
```

结果说明：


|    code   |   msg    |  说明     |
|-----------|----------|----------|
|   1      |  not exist  | 未关注   |
|   2      |  exist    | 已关注   |

### 领取礼盒

url: `opengift.json`


返回字段说明：

```
{
  info: {
    ok: true,
    code: '1',
    msg: 'message'
  },
  data: {
    awardName: '奖品1'
  }
}
```

结果说明：


|    code   |   msg    |  说明     |
|-----------|----------|----------|
|  2      |  success  | 获得奖品   |
|  3      |  opened    | 已领取   |


## 最直接的方案：

html:

```
<div class="gift"></div>
```

javascript(使用jQuery):

```
$('.gift').on('click', function (e) {
  $.ajax({
    url: 'checkFollow.json' // 检查是否关注
  })
  .done(function (data) {
    switch (data.info.code) {

      // 未关注
      case '1':
        $.ajax({
          url: 'follow.json'
        })
        .done(function (data) {
          switch (data.info.code) {

            case '1':
              $.ajax({
                url: 'opengift.json'
              })
              .done(function (data) {
                switch (data.info.code) {

                  case: '1':
                    console.log(data.data.awardName);
                    break;

                  case: '2':
                    console.log('opened');
                    break;

                }
              });
              break;

            case '2':
              console.log('follow fail');
              break;
          }
        });
        break;

      // 已关注，开礼盒
      case '2':
        $.ajax({
          url: 'opengift.json'
        })
        .done(function (data) {

          switch (data.info.code) {

            case '1':
              console.log(data.data.awardName);
              break;

            case '2':
              console.log('opened');
              break;
          }
        });
        break;
    }
  });
});
```

上面的代码把所有工作都放到了`click`监听器中执行，单一满足现在的需求还勉强能接受。但是存在一些问题：

- 打开礼盒这个逻辑在两个地方重复出现，如果第三个地方出现这个功能，那必须重写一遍，假设有10个地方用到，就需要复制10遍。第二天客户说要增加一种礼盒开奖结果。那么需要修改10个地方
- 所有的ajax使用switch处理不同情况，然后再嵌套ajax， switch很容易产生混乱，code的不同编码没有包含任何语意，需要多处添加注释，否则需要回去查阅API文档
- 如果开礼盒引入积分规则，关注之后需要判断积分是否足够才能开礼盒，需要修改的地方就更多了
- 如果页面其他地方需要使用关注，检测关注，开礼盒，代码需要重复复制
- 如果接口升级需要更换地址，需要到代码中到处搜索替换


目前简单想到的就是上面这些问题，所以在一开始的时候保持一个良好的代码结构能够让后续维护更加容易。下面是目前个人水平下能够想到的一些重构方法：

- 根据功能划分不同模块，如`util`，`ctrl`, `app`
- 将不同功能封装到对应模块

下面是修改之后的逻辑：

```
// 配置参数
var config = {
  // 接口地址
  api: {
    follow: 'follow.json',
    checkFollow: 'checkFollow.json',
    openGift: 'opengift.json'
  }
};

// 封装基础操作
var util = {};

// 空函数，默认参数常用
util.noop = function () {};


/**
 * 检查当前用户是否关注，然后执行对应操作
 * arg {Object} 配置回调，内容见defaults
 **/
util.checkFollow = function (arg) {
  var opt = $.extend({}, util.checkFollow.defaults, arg);

  $.ajax({
    url: config.api.checkFollow,
    data: opt.data
  })
  .done(function (data) {
    switch (data.info.code) {

      case '1':
        opt.onNotFollow(data);
        break;

      case '2':
        opt.onFollowed(data);
        break;
    }
  });
};
util.checkFollow.defaults = {
  data: {},   // 请求接口时带的参数
  onNotFollow: util.noop,    // 没有关注
  onFollowed: util.noop     // 已关注
};


/**
 * 执行关注操作，调用对应回调
 * arg {Object} 设置回调，内容见defaults
 **/
util.follow = function (arg)) {
  var opt = $.extend({}, util.follow.defaults, arg);

  $.ajax({
    url: config.api.follow,
    data: opt.data
  })
  .done(function (data) {
    switch (data.info.code) {
      case '1':
        opt.onSuccess(data);
        break;

      case '2':
        opt.onFail(data);
        break;
    }
  });
};
util.follow.defaults = {
  data: {},
  onSuccess: util.noop,   // 关注成功
  onFail: util.noop      // 关注失败
};

/**
 * 打开礼盒
 * arg {Object} 设置回调和参数
 **/
util.openGift = function (arg) {
  var opt = $.extend({}, util.openGift.defaults, arg);

  $.ajax({
    url: config.api.openGift,
    data: opt.data
  })
  .done(function (data) {
    switch (data.info.code) {

      case '1':
        opt.onSuccess(data);
        break;

      case '2':
        opt.onOpened(data);
        break;
    }
  });
};
util.openGift.defaults = {
  data: {},
  onSuccess: util.noop,   // 获得奖品
  onOpened: util.noop    // 已领取
};


var ctrl = {};

var app = {};

app.init = function () {
  $('.gift').on('click', function (e) {
    util.checkFollow({
      onNotFollow: function () {
        util.follow({
          onSuccess: function () {
            util.openGift({
              onSuccess: function (data) {
                console.log(data.data.awardName);
              }
            });
          }
        });
      },

      onFollowed: function () {
        util.openGift({
          onSuccess: function (data) {
            console.log(data.data.awardName);
          }
        });
      }
    });
  });
};
```

上面的代码将基本的ajax请求根据接口逻辑进行了一层封装，提高代码复用度，结构也更清晰。基本上可以满足需求了。

但是设置了大量异步回调似乎还有可以优化的地方。

## 。。



[1]: http://7xio0w.com1.z0.glb.clouddn.com/QQ20150623-1@2x.png
