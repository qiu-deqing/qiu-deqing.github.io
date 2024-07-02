---
title: Apache服务器
---

# 虚拟主机

虚拟主机是指一个机器上运行多个网站(例如a.example.com和b.example.com). 虚拟主机

## mac配置虚拟主机


1. 绑定本机host: `127.0.0.1 qiu.localhost.com`
2. 修改Apache主配置文件`/private/etc/apache2/httpd.conf`, 去掉虚拟主机配置文件前面的注释符
    ```
    # Virtual hosts
    Include /private/etc/apache2/extra/httpd-vhosts.conf
    ```
3. 新建文件`/Library/WebServer/Documents/qiu/index.php`内容如下
    ```
    <?php phpinfo();
    ```
4. 在虚拟主机配置文件`/private/etc/apache2/extra/httpd-vhosts.conf`中添加一个虚拟主机配置
    ```
    <VirtualHost *:80>
        DocumentRoot "/Library/WebServer/Documents/qiu"
        ServerName qiu.localhost.com
    </VirtualHost>
    ```
5. 重启Apache服务器
    ```
    sudo apachectl restart
    ```
6. 浏览器访问`http://qiu.localhost.com/`看到php版本信息表示成功

以上例子为本机环境, 线上环境将域名解析到主机ip即可
