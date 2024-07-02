---
title: 快乐慕商城视频
---

# 环境学习

- [Linux权限管理之基本权限](http://www.imooc.com/learn/481)
- [Linux软件安装管理](http://www.imooc.com/learn/447)
- [Linux达人养成计划](http://www.imooc.com/learn/175)
- [Linux服务管理](http://www.imooc.com/learn/537)
- [学习iptables防火墙](http://www.imooc.com/learn/389)
- [Tomcat基础](http://www.imooc.com/learn/166)
- [Maven基础](http://www.imooc.com/learn/443)
- [MySQL基础](http://www.imooc.com/learn/122)

[视频地址](https://coding.imooc.com/lesson/96.html#mid=3287)

# centos软件源配置

阿里云帮助: [http://mirrors.aliyun.com/help/centos]()

1. 备份
2. 下载新的`CentOS-Base.repo`到`/etc/yum.repos.d/`
3. 运行``生成缓存
  ```
  mv mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
  curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
  yum makecache
  ```


# 查看centos是32位还是64位

`uname -a`

# centos 安装oracle java 8

[rpm vs yum](https://www.ibm.com/developerworks/library/l-lpic1-102-5/index.html)

1. 清理系统默认自带jdk:`rpm -qa | grep jdk`查看自带的jdk,执行`sudo yum remove xxx`
2. 切换到需要保存安装包的目录如根目录下载jre rpm包
3. 安装到本地,通常目录为`/usr/java/jdk1.8.xxx_xx/jre/bin/java`并连接到`/usr/bin/java`

  ```
  cd ~

  wget --no-cookies --no-check-certificate \
  --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" \
  "http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.rpm"

  rpm -ivh jdk-8u151-linux-x64.rpm
  rm ~/jdk-8u151-linux-x64.rpm

  ```

4. 配置jdk环境变量`vim /etc/profile`,添加

  ```
  export JAVA_HOME=/usr/java/jdk1.8.0_151
  export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
  export PATH=$PATH:$JAVA_HOME/bin
  ```

5. `source /etc/profile`生效


[视频地址](https://coding.imooc.com/lesson/96.html#mid=3288)
