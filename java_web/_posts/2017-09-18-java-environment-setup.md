---
title: Java开发环境搭建
---

# 确认本机使用的shell

1. 执行`echo $SHELL`
2. 输出`/bin/bash`为bash, 环境变量配置文件为`~/.bash_profile`
3. 输出`/bin/zsh`为zsh, 配置文件为`~/.zshrc`


# 安装jdk

1. oracle官网下载jdk安装文件安装
2. `/usr/libexec/java_home -V`查看本机所有jdk
3. `vim ~/.zshrc`:

    ```
    export JAVA_HOME=$JAVA_8_HOME
    alias jdk8="export JAVA_HOME=$JAVA_8_HOME"
    PATH=$PATH:$JAVA_HOME/bin
    CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    ```
4. `source ~/.zshrc`


# 安装maven

1. Apache官网下载maven解压到本地
2. `vim ~/.zshrc`添加:

    ```
    # maven
    export M3_HOME=/Users/songmengjiao/Desktop/tool/apache-maven-3.5.0
    export M3=$M3_HOME/bin
    export PATH=$M3:$PATH
    ```
3. `source ~/.zshrc`
4. `mvn -v`显示版本信息
5. (可选)修改maven本地仓库路径:修改`M3_HOME/conf/settings.xml`下`<localRepository></localRepository>`到制定目录.

# 安装Tomcat

1. 下载解压Tomcat到本地
2. 配置`vim ~/.zshrc`

    ```
    # tomcat
    CATALINA_HOME=/Users/songmengjiao/Desktop/tool/apache-tomcat-9.0.0.M26
    CATALINA_BASE=$CATALINA_HOME
    ```
3. `source ~/.zshrc`




http://www.codingpedia.org/ama/how-to-prepare-the-macbook-pro-for-java-development-and-more/
