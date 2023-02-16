---
title: github, gitlab设置多个SSH Key
---

github, gitlab提交需要身份验证, 使用ssh key可以避免每次都输入账号密码, 可以在本机为github和gitlab分别生成ssh key.

# 生成第一个ssh key

命令行执行以下命令

    ssh-keygen -t rsa -C "youremail@yourcompany.com"

一直回车ssh生成key默认会在`~/.ssh`生成`id_rsa`, `id_rsa.pub`两个文件, 分别保存私钥和公钥, 复制公钥`id_rsa.pub`内容到github/gitlab设置ssh key.

执行以下命令添加私钥

    ssh-add ~/.ssh/id_rsa

# 生成第二个ssh key

命令行再次执行

    ssh-keygen -t rsa -C "youremail@yourcompany.com"

因为系统已经有一个默认key了. 系统会让你输入key保存文件名, 可以输入`/Users/qiudeqing/.ssh/id_rsa_github.pub`, 然后一直回车.

系统会在指定路径生成`id_rsa_gitlab`, `id_rsa_gitlab.pub`两个文件, 复制公钥`id_rsa_gitlab.pub`内容到github/gitlab设置ssh key


执行以下命令添加私钥

    ssh-add ~/.ssh/id_rsa_gitlab


# 给远程服务器添加ssh公钥

1. 在服务器`~/.ssh/authorized_keys`文件内添加用户的SSH公钥
2. 执行`chmod 600 ~/.ssh/authorized_keys`

现在用ssh连接就不需要密码了

# ssh退出命令

    exit

    

# 参考资料

- [github/gitlab管理多个ssh key][1]


[1]: http://www.cnblogs.com/fanyong/p/3962455.html
