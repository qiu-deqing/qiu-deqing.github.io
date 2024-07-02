---
title: browserify相关
---

[browserify][1]是一个将CommonJS模块编译为浏览器模块的工具.

browserify使用的模块系统与Nodejs相同, 所以通过npm发布的Nodejs模块都可以通过browserify编译到浏览器环境下使用.

# 安装

```
npm install -g browserify
```

# Hello World

```
mkdir test
cd test
npm init
npm install uniq --save
vim main.js
browserify main.js -o bunddle.js
vim index.html
```

main.js:

```
var unique = require('uniq');
var data = [1, 2, 2, 3, 3, 4, 5];
console.log(unique(data));    // [1, 2, 3, 4, 5]
```

index.html:

```
<script src="bundle.js"></script>
```

[2]: https://github.com/substack/browserify-handbook
[1]: http://browserify.org/
