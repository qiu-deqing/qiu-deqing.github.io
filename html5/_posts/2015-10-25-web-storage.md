

- `sessionStorage`保存的数据在页面关闭,刷新后就丢失
- `localStorage`保存的数据一直存在, 除非特意清除

两种对象操作一样, 不发送给服务器, key/value保存, key可以是数字或者字符串, value只能是字符串

[Storage API][3]


## 保存值

以下三种方法都可以

```
localStorage.colorSetting = '#fff';

localStorage['colorSetting'] = '#fff';

localStorage.setItem('colorSetting', '#fff');
```

## 读取值

```
localStorage.getItem('colorSetting');
```

## 删除记录

```
localStorage.removeItem('colorSetting');

localStorage.clear();
```

## 检测是否可用

由于很多浏览器包含**隐身模式**, 不同浏览器在这种模式下对`localStorage`实现不一样, 以下为[Modernizr 3.0][2]检测方法

```
Modernizr.addTest('localstorage', function () {
  var mod = 'modernizr';
  try {
    localStorage.setItem(mod, mod);
    localStorage.removeItem(mod);
    return true;
  } catch (e) {
    return false;
  }
});
```


## 参考资料

- [https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API][1]


[1]: https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API
[2]: https://modernizr.com/
[3]: https://developer.mozilla.org/en-US/docs/Web/API/Storage
