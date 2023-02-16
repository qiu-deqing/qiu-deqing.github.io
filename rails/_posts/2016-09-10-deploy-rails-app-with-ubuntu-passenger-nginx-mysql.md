---
title: 在Ubuntu上部署rails应用
---

本文在新安装的Ubuntu 14.04 x86

# 系统准备

安装以下工具

- curl
- gpg
- 编译工具

运行以下命令:

    sudo apt-get update
    sudo apt-get install -y curl gnupg build-essential


# 安装RVM


    gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    curl -sSL https://get.rvm.io | sudo bash -s stable
    sudo usermod -a -G rvm `whoami`

重新登录系统`rvm -v`显示版本信息

# 安装所需版本的Ruby


通常安装最新版本即可

    rvm install ruby
    rvm --default use ruby

或者安装指定版本

    rvm install ruby-X.X.X
    rvm --default use ruby-X.X.X


# 安装Bundler

    gem install bundler --no-rdoc --no-ri

# 安装Node.js

Rails asset pipeline compiler需要java运行时环境, Node.js具体版本无所谓

    sudo apt-get install -y nodejs
    sudo ln -sf /usr/bin/nodejs /usr/local/bin/node


# sudo vs rvmsudo

One thing you should be aware of when using RVM, is that you should use rvmsudo instead of sudo when executing Ruby-related commands. This is because RVM works by manipulating environment variables. However, sudo nukes all environment variables for security reasons, which intereferes with RVM.

[Visit the RVM website to learn more about rvmsudo.][1]


# 安装Git

我们通过git将app拉到本地

    sudo apt-get install -y git


# 安装Passenger和Nignx



1. 安装passenger和nginx包

    These commands will install Passenger + Nginx through Phusion's APT repository. If you already had Nginx installed, then these commands will upgrade Nginx to Phusion's version (with Passenger compiled in).

        # install our pgp key and add https support for apt
        sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
        sudo apt-get install -y apt-transport-https ca-certificates

        # Add our APT repository
        sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger trusty main > /etc/apt/sources.list.d/passenger.list'
        sudo apt-get update

        # Install Passenger + Nginx
        sudo apt-get install -y nginx-extras passenger

2. 配置Passenger Nignx module并且重启Nginx

    编辑`/etc/nginx/nginx.conf`,并且去掉`include /etc/nginx/passenger.confg;`前面的注释

    重启Nginx

        sudo service nginx restart

3. 检查安装

    执行以下命令检查passenger是否成功安装

        sudo /usr/bin/passenger-config validate-install

    结果大致如下:

        What would you like to validate?
        Use <space> to select.
        If the menu doesn't display correctly, press '!'

         > (*)  Passenger itself
           ( )  Apache

        -------------------------------------------------------------------------

         * Checking whether this Passenger install is in PATH... ✓
         * Checking whether there are no other Passenger installations... ✓

        Everything looks good. :-)

    执行以下命令检查Nginx是否正确启动Passenger

        sudo /usr/sbin/passenger-memory-stats

    结果大致如下:

        Version: 5.0.30
        Date   : 2016-09-10 06:45:25 -0400

        ------- Apache processes --------

        ### Processes: 0
        ### Total private dirty RSS: 0.00 MB


        ---------- Nginx processes ----------
        PID    PPID   VMSize   Private  Name
        -------------------------------------
        31096  1      51.5 MB  0.2 MB   nginx: master process /usr/sbin/nginx
        31103  31096  51.7 MB  0.4 MB   nginx: worker process
        ### Processes: 2
        ### Total private dirty RSS: 0.55 MB


        ---- Passenger processes -----
        PID    VMSize   Private  Name
        ------------------------------
        31077  30.7 MB  0.7 MB   Passenger watchdog
        31080  51.0 MB  0.9 MB   Passenger core
        31085  40.8 MB  0.8 MB   Passenger ust-router
        ### Processes: 3
        ### Total private dirty RSS: 2.48 MB

4. 定期更新

Nginx, Passenger通过APT包管理工具发布更新, 你应该经常运行以下命令以保持他们最新

        sudo apt-get update
        sudo apt-get upgrade

更新之后, 不需要重启Nginx或者Passenger, 也不需要修改配置文件, APT会自动处理.


# 部署APP到服务器

前面都是在服务器安装部署所需环境, 现在部署rails app到服务器.

## 1. 将app代码提交到服务器

1.1 将代码部署到Git仓库
这里我部署了一个demo app到[os china][4]

## 2. 登录到服务器创建管理app的用户

不同app由不同用户管理能让系统更安全

## 3. 将app代码从git仓库拉到服务器

需要在服务器上选择一个保存app代码的目录, 通常选择`/usr/www/<app_name>`

    mkdir -p /var/www/hello-app
    cd /var/www/hello-app
    git clone git@git.oschina.net:qiudeqing/hello-app.git code

现在项目代码位于`/var/www/hello-app/code`目录下

## 4. 安装app依赖的包

    bundle install --deployment --without development test

## 5. 配置database.yml和secrets.yml

以SQLite为例, `vim config/database.yml`

    default: &default
      adapter: sqlite3
      pool: 5
      timeout: 5000

    development:
      <<: *default
      database: db/development.sqlite3

    # Warning: The database defined as "test" will be erased and
    # re-generated from your development database when you run "rake".
    # Do not set this db to the same as development or production.
    test:
      <<: *default
      database: db/test.sqlite3

    production:
      <<: *default
      database: db/production.sqlite3

Rails also needs a unique secret key with which to encrypt its sessions. Starting from Rails 4, this secret key is stored in config/secrets.yml. But first, we need to generate a secret key. Run:

    bundle exec rake secret

上面的命令会生成一个secret key, 复制内容到`confg/secrets.yml`文件的product -> secret_key_base字段

    production:
      secret_key_base: the value that you copied from 'rake secret'

设置文件权限防止其他用户读写

    chmod 700 config db
    chmod 600 config/database.yml config/secrets.yml

## 6. 编译Rails assets并运行数据库migrations

    bundle exec rake assets:precompile db:migrate RAILS_ENV=production


# 配置Nginx和Passenger

## 1. 设置Passenger使用的Ruby命令

设置Passenger使用ruby版本可以保证多个版本ruby存在时系统正确运行

    passenger-config about ruby-command

结果大致如下:

    passenger-config was invoked through the following Ruby interpreter:
      Command: /usr/local/rvm/gems/ruby-2.3.0/wrappers/ruby
      Version: ruby 2.3.0p0 (2015-12-25 revision 53290) [i686-linux]
      To use in Apache: PassengerRuby /usr/local/rvm/gems/ruby-2.3.0/wrappers/ruby
      To use in Nginx : passenger_ruby /usr/local/rvm/gems/ruby-2.3.0/wrappers/ruby
      To use with Standalone: /usr/local/rvm/gems/ruby-2.3.0/wrappers/ruby /usr/bin/passenger start


    ## Notes for RVM users
    Do you want to know which command to use for a different Ruby interpreter? 'rvm use' that Ruby interpreter, then re-run 'passenger-config about ruby-command'.

其中**Command**后面的内容将在后面使用如: `/usr/local/rvm/gems/ruby-2.3.0/wrappers/ruby`

## 2. 返回root账号设置nginx

前面使用为app创建的账号登录来准备app环境, 这个账号没有sudo权限, 现在需要返回root权限用户以设置nginx

## 3. 编辑nginx配置文件

我们需要为app创建一个nginx配置文件, 用来设置虚拟主机入口, 这个虚拟主机入口告诉nginx你的app在哪里.

    vim /etc/nginx/sites-enabled/myapp.conf

内容如下:

    server {

      listen 80;
      server_name blog.qiudeqing.com;
      root /var/www/blog/code/public;
      passenger_enabled on;
      passenger_ruby /usr/local/rvm/gems/ruby-2.3.0/wrappers/ruby;

    }


重启nginx

    service nginx restart

## 4. 检查服务器

    crul http://blog.qiudeqing.com

显示首页信息表示成功. 注意这里需要设置DNS解析

# Passenger + Nginx环境日志

默认日志文件路径: `/var/log/nginx/error.log`

[官方文档][5]



# 参考资料

- [https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/ownserver/nginx/oss/install_language_runtime.html][2]
- [https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/ownserver/nginx/oss/trusty/deploy_app.html][3]



[5]: https://www.phusionpassenger.com/library/admin/nginx/log_file/
[4]: https://git.oschina.net/qiudeqing/hello-app
[3]: https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/ownserver/nginx/oss/trusty/deploy_app.html
[2]: https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/ownserver/nginx/oss/install_language_runtime.html
[1]: https://rvm.io/integration/sudo
