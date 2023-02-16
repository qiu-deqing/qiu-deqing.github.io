---
title: shadowsocks基础
---

# 科学上网漫游指南

https://lvii.gitbooks.io/outman/content/ss.mac.html


# Ubuntu 16安装shadowsocks服务器

1. 执行命令

    ```
    sudo apt-get update && sudo apt-get install shadowsocks
    ```
    
2. 配置`/etc/shadowsocks/config.json`, 修改`server`为`0.0.0.0`, `password`为你需要设置的值

    ```
    {
        "server":"your_server_ip",
        "server_port":8388,
        "local_address": "127.0.0.1",
        "local_port":1080,
        "password":"your_password",
        "timeout":300,
        "method":"aes-256-cfb",
        "fast_open":false,
        "workers":1,
    }
    ```
3. 启动服务: `sudo service shadowsocks restart`


# 参考资料

- [Setup-Shadowsocks-in-Ubuntu-16-04][1]

[1]: https://panlong.me/2016/06/20/Setup-Shadowsocks-in-Ubuntu-16-04/