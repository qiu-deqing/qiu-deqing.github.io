---
title: rails tutorial笔记
---

出处[https://www.railstutorial.org/book/beginning][1]

# chapter1: From zero to deploy


新建空项目

    rails new hello_app

启动项目服务器

    rails server

## bundler管理gem

gem最小版本的修改都可能使程序崩溃,为了方便维护, Gemfile中需要始终写死确定版本.[教程初始的Gemfile][2]


修改好文件之后安装Gemfile配置的gem, 我们将数据库改为mysql: `gem "mysql", "2.9.1"`

    bundle install




[2]: https://github.com/mhartl/rails_tutorial_4th_edition_gemfiles/blob/master/hello_app/Gemfile
[1]: https://www.railstutorial.org/book/beginning
