---
title: 文件上传下载
---

# 文件下载原理

1. 使用`HttpServletResponse.setContentType`设置`Content-Type`头字段值为浏览器无法使用某种方式或激活某个程序来处理的MIME类型,例如`application/octet-ostream`或`application/x-msdownload`
2. 通过`HttpServletResponse.setHeader`方法设置`Content-Disposition`值为`attachment;filename=<文件名>`
3. 读取下载文件, 调用`HttpServletResponse.getOutputStream`方法返回的`ServletOutputStream`对象向客户端写入文件内容
