---
title: github托管jekyll网站
---

[github官方教程][1]


# 环境搭建

1. 安装`ruby`
2. 安装bundler: `gem install bundler`
3. 如果是项目网站, 切换到gh-pages分支: `git checkout -b gh-pages`
4. 检查项目根目录下是否有`Gemfile`文件, 如果没有,创建并填写以下内容:
```
gem 'github-pages', group: :jekyll_plugins
```
5. 安装依赖`bundler install`
6. 运行`bundler exec jekyll serve`



[1]: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/
