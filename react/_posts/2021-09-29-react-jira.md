---
title: 慕课网react jira学习笔记
---

[慕课网react jira学习笔记](https://coding.imooc.com/learn/list/482.html)

[源码](https://github.com/qiu-deqing/react-jira)

# 第二章 项目起航：项目初始化与配置

## 2.1 用create react app初始化项目

`npx create-react-app react-jira --template typescript`

## 2.2 eslint和commitlint规范工程

1. `tsconfig.json`配置`baseUrl`
```
{
  "compilerOptions": {
    "baseUrl": "./src",
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": [
    "src"
  ]
}
```

2. 配置格式化工具`prettier`

## 2.3 对比常见Mock方案，配置JSON SERVER

1. `npm install json-server -D`
2. 根目录新建文件`__json_server_mock__/db.json`
```
{
  "users": [
    {
      "id": 1,
      "name": "高修文"
    },
    {
      "id": 2,
      "name": "熊天成"
    },
    {
      "id": 3,
      "name": "郑华"
    },
    {
      "id": 4,
      "name": "王文静"
    }
  ],
  "projects": [
    {
      "id": 1,
      "name": "骑手管理",
      "personId": 1,
      "organization": "外卖组",
      "created": 1604989757139
    },
    {
      "id": 2,
      "name": "团购 APP",
      "personId": 2,
      "organization": "团购组",
      "created": 1604989757139
    },
    {
      "id": 3,
      "name": "物料管理系统",
      "personId": 2,
      "organization": "物料组",
      "created": 1546300800000
    },
    {
      "id": 4,
      "name": "总部管理系统",
      "personId": 3,
      "organization": "总部",
      "created": 1604980000011
    },
    {
      "id": 5,
      "name": "送餐路线规划系统",
      "personId": 4,
      "organization": "外卖组",
      "created": 1546900800000
    }
  ]
}
```
3. `package.json`下配置`scripts`添加：
```
"json-server": "json-server __json_server_mock__/db.json --watch --port 3001"
```

## 2.4 不手动引入React

https://coding.imooc.com/lesson/482.html#mid=41564

编译工具`plugin-transform-react-jsx` 从v7.9.0开始不用手动引入React默认关闭，create-react-app 4.0开始默认开启，所以不用引入React也可以。


## 3.2 组件状态提升

### 配置不同运行环境的变量如`baseUrl`



1. 在项目根目录添加配置文件`.env`, `.env.development`填写内容
```
REACT_APP_BASE_URL=http://localhost:3001
```

2. 代码中读取`const baseUrl = process.env.REACT_APP_BASE_URL`，`npm start`时读取`.env.development`内的变量，`npm run build`读取`.env`内变量

注意：在create-react-app中使用时，变量名需以`REACT_APP_`开头
