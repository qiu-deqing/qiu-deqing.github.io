---
title: 表单参数获取

---

## 需求1.0

一个用户注册页面,要求用户在表单中填写姓名,年龄,电子邮箱,个人说明.点击提交之后ajax发送到后台进行保存.

接口'/user'参数如下

```
{
  "name": "name",
  "age": 22,
  "email": "qiu@126.com",
  "desc": "说明"
}
```

## 版本1.0

```
<form action="#" class="user-info">
  <div>
    <label>姓名:<input type="text" name="name"></label>
  </div>
  <div>
    <label>年龄:<input type="text" name="age"></label>
  </div>
  <div>
    <label>电子邮箱<input type="text" name="email"></label>
  </div>
  <div>说明:
    <textarea name="desc"></textarea>
  </div>
  <div>
    <button type="submit" class="btn-submit">确定注册</button>
  </div>
</form>

<script>
var userInfo = $('.user-info');
userInfo.on('submit', function (e) {
  e.preventDefault();

  $.ajax({
    url: '/user',
    data: userInfo.serialize()
  })
  .done(function (resp) {
    console.log('注册成功');
  });
})
</script>
```

实现了需求,但是代码揉在一起, 可维护性, 测试性差, 可以优化.

## 版本2.0

```
<script>
var userManager = {
  url: '/user',

  init: function () {
    var me = this;

    me.infoForm = $('.user-info');
    me.infoForm.on('submit', me.onFormSubmit);
  },

  onFormSubmit: function (e) {
    e.preventDefault();
    var form = $(e.target);
    var userInfo = userManager.getUserInfo(form);
    userManager.addUser({
      url: userManager.url,
      data: userInfo
    });
  },

  getUserInfo: function (infoForm) {
    var infoArray = infoForm.serializeArray();
    var userInfo = {};
    infoArray.forEach(function (infoItem) {
      userInfo[infoItem.name] = infoItem.value;
    });
    return userInfo;
  },

  addUser: function (opt) {
    $.ajax(opt)
      .done(function (resp) {
        console.log(resp)
        console.log('注册成功');
      });
  }
};

userManager.init();
</script>
```

把代码拆分开来,增加了代码,简单的事情复杂化. 但是可测试性和可维护性提高了,
现在要增加下面的扩展需求也就容易了.

## 需求2.1:

1. **参数校验**: 姓名不能为空且长度不超过10, 年龄取值区间[1, 99], 邮箱需符合基本格式,
验证不通过不能提交, 需要提示用户验证失败原因

在2.0的基础上添加一个验证函数即可.

```
<script>
var userManager = {
  url: '/user',

  init: function () {
    var me = this;

    me.infoForm = $('.user-info');
    me.infoForm.on('submit', me.onFormSubmit);
  },

  onFormSubmit: function (e) {
    e.preventDefault();
    var form = $(e.target);
    var userInfo = userManager.getUserInfo(form);

    var infoStatus = userManager.validateUserInfo(userInfo);

    if (!infoStatus.ok) {
      var statusMsg = infoStatus.details.map(function (statusItem) {
        return statusItem.msg;
      }).join(',');
      console.log(statusMsg);
      return;
    }
    userManager.addUser({
      url: userManager.url,
      data: userInfo
    });
  },

  getUserInfo: function (infoForm) {
    var infoArray = infoForm.serializeArray();
    var userInfo = {};
    infoArray.forEach(function (infoItem) {
      userInfo[infoItem.name] = infoItem.value;
    });
    return userInfo;
  },

  validateUserInfo: function (userInfo) {
    var infoStatus = {
      ok: true,
      details: []
    };

    var name = userInfo.name;
    if (!(name && name.length <= 10)) {
      infoStatus.ok = false;
      infoStatus.details.push({
        field: 'name',
        msg: '姓名不能为空,且长度不超过10'
      });
    }

    var age = parseInt(userInfo.age, 10);
    if (isNaN(age) || age < 1 || age > 99) {
      infoStatus.ok = false;
      infoStatus.details.push({
        field: 'age',
        msg: '年龄取值应该在1到99之间'
      });
    }

    // 邮箱规则测试只是用来举例
    var email = userInfo.email;
    if (!email || !/\.com$/.test(email)) {
      infoStatus.ok = false;
      infoStatus.details.push({
        field: 'email',
        msg: '电子邮箱不符合规则'
      });
    }
    return infoStatus;
  },

  addUser: function (opt) {
    $.ajax(opt)
      .done(function (resp) {
        console.log(resp)
        console.log('注册成功');
      });
  }
};

userManager.init();
</script>
```

2.1版本添加了`validateUserInfo`方法, 对收集到的用户信息每一个字段进行校验,如果有一个
字段失败,就会设置状态为失败,并且添加失败的详细信息,供调用程序查看以了解情况.

对于调用程序来说, 执行验证之后采取不同的操作就变得容易. 一个反面的验证实现是将验证代码写到
onFormSubmit中如下:

```
onFormSubmit: function (e) {
  e.preventDefault();
  var form = $(e.target);
  var userInfo = userManager.getUserInfo(form);

  var name = userInfo.name;
  if (!(name && name.length <= 10)) {
    alert('姓名不能为空,且长度不超过10');
    return;
  }

  var age = parseInt(userInfo.age, 10);
  if (isNaN(age) || age < 1 || age > 99) {
    alert('年龄取值应该在1到99之间');
    return;
  }

  // 邮箱规则测试只是用来举例
  var email = userInfo.email;
  if (!email || !/\.com$/.test(email)) {
    alert('电子邮箱不符合规则');
    return;
  }

  userManager.addUser({
    url: userManager.url,
    data: userInfo
  });
}
```

上面的实现将验证和验证操作耦合在一起并且都写死在onFormSubmit中,
逻辑修改变得困难,测试也不方便, 没有保留任何的校验信息, 页面中其他地方需要校验信息时只能
再次获取(如:发送校验信息到服务器以统计用户行为).

## 需求2.2

1. **个人说明**: 如果个人说明为空,不传这个字段到后端, 或者要求传递一个默认说明.
2. **年龄**: 提交到后端时年龄需要传递整数而不是字符串

这样的需求也很常见.

```
<script>
var userManager = {
  url: '/user',

  init: function () {
    var me = this;

    me.infoForm = $('.user-info');
    me.infoForm.on('submit', me.onFormSubmit);
  },

  onFormSubmit: function (e) {
    e.preventDefault();
    var form = $(e.target);
    var userInfo = userManager.getUserInfo(form);

    var infoStatus = userManager.validateUserInfo(userInfo);

    if (!infoStatus.ok) {
      var statusMsg = infoStatus.details.map(function (statusItem) {
        return statusItem.msg;
      }).join(',');
      console.log(statusMsg);
      return;
    }

    var userParam = userManager.wrapUserParam(userInfo);

    userManager.addUser({
      url: userManager.url,
      data: userParam
    });
  },

  wrapUserParam: function (userInfo) {
    var userParam = {
      'name': userInfo.name,
      'email': userInfo.email
    };

    var age = parseInt(userInfo.age);
    userParam['age'] = isNaN(age) ? 1 : age;

    // 如果后端要求不传这个字段,就不需要下面这个if判断了
    // wrapUserParam只收集需要的信息并做必要转换
    if (!userInfo.desc) {
      userParam['desc'] = '这个人很懒,什么也没留下';
    }

    return userParam;
  },

  getUserInfo: function (infoForm) {
    var infoArray = infoForm.serializeArray();
    var userInfo = {};
    infoArray.forEach(function (infoItem) {
      userInfo[infoItem.name] = infoItem.value;
    });
    return userInfo;
  },

  validateUserInfo: function (userInfo) {
    var infoStatus = {
      ok: true,
      details: []
    };

    var name = userInfo.name;
    if (!(name && name.length <= 10)) {
      infoStatus.ok = false;
      infoStatus.details.push({
        field: 'name',
        msg: '姓名不能为空,且长度不超过10'
      });
    }

    var age = parseInt(userInfo.age, 10);
    if (isNaN(age) || age < 1 || age > 99) {
      infoStatus.ok = false;
      infoStatus.details.push({
        field: 'age',
        msg: '年龄取值应该在1到99之间'
      });
    }

    // 邮箱规则测试只是用来举例
    var email = userInfo.email;
    if (!email || !/\.com$/.test(email)) {
      infoStatus.ok = false;
      infoStatus.details.push({
        field: 'email',
        msg: '电子邮箱不符合规则'
      });
    }
    return infoStatus;
  },

  addUser: function (opt) {
    $.ajax(opt)
      .done(function (resp) {
        console.log(resp)
        console.log('注册成功');
      });
  }
};

userManager.init();
</script>
```

2.2版本添加了`wrapUserParam`方法收集并转换需要的参数.

## 需求2.3

1. **接口升级**: 参数修改如下

    ```
    {
      "user.name": "name",
      "user.age": 22,
      "user.email": "qiu@126.com",
      "user.desc": "说明"
    }
    ```

有了前面的`wrapUserParam`这个修改就很容易了, 直接在这里修改`userParam`的key就可以了,
也可以在里面加一个user对象,就少写几次user了

```
wrapUserParam: function (userInfo) {
  var userParam = {
    'user.name': userInfo.name,
    'user.email': userInfo.email
  };

  var age = parseInt(userInfo.age);
  userParam['user.age'] = isNaN(age) ? 1 : age;

  // 如果后端要求不传这个字段,就不需要下面这个if判断了
  // wrapUserParam只收集需要的信息并做必要转换
  if (!userInfo.desc) {
    userParam['user.desc'] = '这个人很懒,什么也没留下';
  }

  return userParam;
}
```

## 需求2.4

1. 将验证不通过字段清空

在之前的`validateUserInfo`的返回结果中已经包含了所有验证失败的信息.
添加一个`promptValidateStatus`函数,接收验证信息,并执行操作即可.
在之前的设计中使用`field`标记了失败域


## ...


## 总结

以上需求都是平时常见的场景, 实现方法局限于本人当前水平, 不保证完全合理.

[最终版本的线上地址][1]


[1]: http://qiudeqing.com/demo/javascript/add-user.html
