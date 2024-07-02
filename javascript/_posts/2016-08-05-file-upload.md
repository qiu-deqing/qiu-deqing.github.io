---
title: 文件上传
---


# 上传文件同时提交字段

需求: form提交用户名, 密码, 头像

```
$.ajax({
    url: url + '?' + paraStr,
    method: 'post',
    contentType: 'application/octet-stream',
    data: fileStr
})
```
