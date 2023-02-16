---
title: svn客户端命令
---

## svn checkout: 从服务器仓库获取项目到本地

`svn checkout`命令用于从SVN服务器仓库获取项目源代码到本地.

语法:

```
svn checkout/co URL PATH
```

其中:

- URL是项目服务器地址
- PATH是远程项目保存到本地的地址, 如果不设置, 会在当前目录下创建项目目录并下载.


## svn status: 查看本地项目文件状态

`svn status`用于查看本地文件状态: 修改, 新增, 删除, 未加入版本控制等.

语法:

```
svn status PATH
```

`svn help status`查看详细帮助


## svn add: 添加文件到版本控制

要提交一个文件到服务器仓库需要两个步骤:

1. svn add将文件添加到版本控制
2. svn commit将文件提交到服务器


## svn 设置ignore列表

设置ignore列表需要使用

```
svn propset svn:ignore RULELIST DIR
```

其中

- RULELIST 是需要ignore文件的规则列表
- DIR 是忽略文件的目录, 如果在项目根目录下, 通常值为`.`

例如我们在项目根目录下设置`node_modules`目录为ignore:

```
svn propset svn:ignore "node_modules" .
```

设置好规则之后commit到服务器仓库即可

### 指定文件为ignore规则

如果项目中已经有了git的`.gitignore`文件, 可以指定它为规则

```
svn propset svn:ignore -F .gitignore .
```
