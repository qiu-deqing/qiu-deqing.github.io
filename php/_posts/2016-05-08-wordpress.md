---
title: wordpress
---

# mac本地安装WordPress

1. 本地安装MySQL, 配置好Apache, php
2. 下载WordPress压缩包,解压到`DocumentRoot`, 通常为`/Library/WebServer/Documents`
3. 启动Apache, 浏览器访问`http://localhost/wordpress`自动跳转到安装页面
4. 填写所需信息即可


# 无法建立目录wp-content/uploads/2016/05。有没有上级目录的写权限

给WordPress目录添加读写权限, 命令进入到WordPress父目录执行以下命令

```
chmod 777 wordpress
```

# 禁用谷歌字体

安装并启用[disable-google-fonts][1]插件

[1]: https://wordpress.org/plugins/disable-google-fonts/

# 如何安装WordPress插件

- 方法一: 下载插件解压到`wp-content/plugins`目录下, 到后台插件列表就可以看见安装好了
- 方法二: 插件管理面板搜索, 安装, 需要填写服务器ftp账号密码
- 方法三: 下载插件到本地,点击上传安装


# 常用插件列表

