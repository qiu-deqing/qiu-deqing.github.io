---
title:  electron react boilerplate安装nodejs native package
---

在项目根目录下安装nodejs native package（如sqlite3)无法使用，`npm install sqlite3`提示

```
Error: Could not locate the bindings file. Tried:
→ /salary/build/node_sqlite3.node
→ /salary/build/Debug/node_sqlite3.node
→ /salary/build/Release/node_sqlite3.node
→ /salary/out/Debug/node_sqlite3.node
→ /salary/Debug/node_sqlite3.node
→ /salary/out/Release/node_sqlite3.node
→ /salary/Release/node_sqlite3.node
→ /salary/build/default/node_sqlite3.node
→ /salary/compiled/20.18.0/darwin/arm64/node_sqlite3.node
```

解决方法：

进入到`release/app`目录下安装并重新构建

```
npm install sqlite3
npm run postinstall
```


