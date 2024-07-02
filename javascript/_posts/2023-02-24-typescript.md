---
title: typescript编程笔记
---

# 初始化项目


## tsconfig.json

项目根目录保存`tsconfig.json`

```
{
  "compilerOptions": {
    "lib": ["es2015"],
    "module": "commonjs",
    "outDir": "dist",
    "sourceMap": true,
    "strict": true,
    "target": "es2015"
  },
  "include": [
    "src"
  ]
}
```

- `include`: TSC在哪个文件夹中寻找typescript文件
- `lib`: TSC假定运行代码的环境中有哪些API？包括ES5的Function.prototype.bind、ES2015的Object.assign和DOM的document.querySelector
- `module`: TSC把代码编译成哪个模块系统（CommonJS、SystemJS、ES2015）
- `outDir`: TSC把生成的JavaScript代码放在哪个文件夹中
- `strict`: 检查无效代码时尽量严格。该选项强制所有代码都正确声明了类型。本书中的所有示例都使用这个选项，你的TypeScript项目也应该这么做。
- `target`： TSC把代码编译成哪个JavaScript版本（ES3、ES5、ES2015、ES2015等）
- `noImplicitAny`：默认情况下TypeScript推导出类型为any时不会报错，配置之后会提示错误信息，如果开启了strict，就不需要设置了。


编写在浏览器中运行的TypeScript时在`lib`中添加`dom`，[TypeScript完整配置](https://www.typescriptlang.org/docs/handbook/compiler-options.html)


## tslint.json

```
{
  "defaultSeverity": "error",
  "extends": [
    "tslint:recommended"
  ],
  "rules": {
    "semicolon": false,
    "trailing-comma": false
  }
}
```

[TSLint规则](https://palantir.github.io/tslint/rules)

添加自定义规则，比如额外的预设:[ReactJS预设]()

# 第三章 数据类型

数据类型结构图
![](https://user-images.githubusercontent.com/125547801/223043546-406d8b40-2f3e-4a37-b6f1-df49998eff63.png)


- 类型别名：
- 并集类型：
- 交集类型：

## null 和 undefined

`undefined`和`null`是所有类型的子类型。也就是说`undefined`类型的变量可以赋值给`number`类型的变量

```
let num: number = undefined;
```

## any

`any`用来表示允许赋值为任意类型

any是TypeScript针对编程时类型不明确的变量使用的一种数据类型，常用于以下三种情况：

- 变量值会动态改变，比如来自用户输入，any可以让这些变量跳过编译阶段的类型检查
- 改写现有代码时，any允许在编译时可选择地包含或移除类型检查
- 定义存储各种类型数据的数组时

## unknown

如果无法预知一个值的类型，使用`unknown`而不是`any`，与any类型，unknown也表示任何值，但是TypeScript会要求你再做检查以细化类型。

unknown支持哪些操作：

```
==
===
||
&&
?
!
typeof
instanceof
```

unknown用法：

- TypeScript不会把任何值推到为unknown类型，必须显式注解
- unknown类型的值可以执行比较操作
- 执行操作时不能假定unknown类型的值为某种特定类型，必须先向TypeScript证明一个值确实是某种类型


## bigint

bigint是JavaScript和TypeScript新引入的类型，在处理较大的整数时，不用担心舍入误差。

## 联合类型（union types）

联合类型表示取值可以为多种类型中的一种

```
let magicNumber : string | number
magicNumber = 'seven'
magicNumber = 7
```

## 接口(interface)

```
interface Person {
  readonly id: number;  // 只读属性：只能在创建的时候被赋值
  name: string;
  age?: number;
  [propName: string]: any;    // 任意属性
}

let tom: Person = {
  name: 'tom',
  age: 25
}
```

一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集。

## 数组

```
let fibonacci: number[] = [1, 1, 2, 3, 5]
let fibonacci: Array<number> = [1, 1, 2, 3, 5]
```

## 函数

```
// 函数声明（Function Declaration）
function sum(x, y) {
  return x + y
}
// 定义
function sum(x: nubmer, y: number): number {
  return x + y
}

// 函数表达式（Fucntion Expression）
let mySum = funciton (x, y) {
  return x + y
}
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
  return x + y
}
```

**注意**：不要混淆了TypeScript中的`=>`和ES6中的`=>`

在TypeScript的类型定义中，`=>`用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

在ES6中，`=>` 是箭头函数。

### 用接口定义函数形状

```
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc
mySearch = function (source: string, subString: string) {
  return source.search(subString) !== -1
}
```

### 可选参数

可选参数必须在必选参数后面，可选参数后面不允许出现必选参数。

```
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + ' ' + lastName
  } else {
    return firstName
  }
}
```

### 参数默认值

在ES6中，我们允许给函数的参数添加默认值，TypeScript会将添加了默认值的参数识别为可选参数，此时不受[可选参数必须在必选参数后]的限制

```
function buildName(firstName: string, lastName: string = 'cat') {
  return firstName + ' ' + lastName
}
```

### 剩余参数

在ES6中，可以用`...rest`的方式获取函数中的剩余参数，rest只能作为最后一个参数。

```
fucntion push(array, ...items) {
  items.forEach(item => {
    array.push(item)
  })
}

// 事实上，items是一个数组，可以用数组类型进行定义
function push(array: any[], ...items: any[]) {
  items.forEach(item => {
    array.push(item)
  })
}
```

### 重载

重载允许一个函数接受不同数量或类型的参数时，做出不同的处理。

比如，`reverse`输入`123`返回`321`，输入`'hello'`时返回`'olleh'`

利用联合类型可以实现：

```
function reverse(x: number | string): number | string | void {
  if (typeof x === 'number') {
    return Number(x.toString().split('').reverse().join(''))
  } else if (typeof x === 'string') {
    return x.split('').reverse().join('')
  }
}
```

这样实现有一个缺点，就是不能精确地表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。使用重载定义多个`reverse`函数类型

```
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string) : number | string | void {
  if (typeof x === 'number') {
    return Number(x.toString().split('').reverse().join(''))
  } else if (typeof x === 'string') {
    return x.split('').reverse().join('')
  }
}
```
我们重复定义了多次reverse，前2次都是函数定义，最后一次是函数实现。TypeScript会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。


## 类型断言（Type Assertion)

类型断言可以用来手动置顶一个值的类型

`<类型>值`或`值 as 类型`(推荐)

在tsx中必须使用`值 as 类型`， `<Foo>`在tsx中表示的是一个`ReactNode`，在ts中也表示泛型

```
var str = '1'
var str2: number = <number><any>str
console.log(str2)
```

### 类型断言的用途

1. 将一个联合类型断言为其中一个类型

```
interface Cat {
  run(): void;
}
interface Fish {
  swim(): void;
}
function isFish(animal: Cat | Fish) {
  if (typeof (animal as Fish).swim === 'function') {
    return true
  }
  return false
}
```

2. 将一个父类断言为更加具体的子类

```
class ApiError extends Error {
  code: number = 0;
}
class HttpError extends Error {
  statusCode: number = 200;
}
fucntion isApiError(error: Error) {
  if (typeof (error as ApiError).code === 'number') {
    return true
  }
  return false
}
```

3. 将任何一个类型断言为`any`

```
(window as any).foo = 1
```

不能滥用`as any`

4. 将`any`断言为具体的类型

### 断言使用限制

- 联合类型可以被断言为其中一个类型
- 父类可以被断言为子类
- 任何类型都可以被断言为any
- any可以被断言为任何类型
- 要使A能够被断言为B，只需要A兼容B或者B兼容A即可

尽量避免使用双重断言`A as any as B`

### 类型断言 vs 类型转换

类型断言只会影响TypeScript编译时的类型，在编辑结果中会被删除

### 类型断言  vs 类型声明

类型声明比类型断言更加严格。优先使用类型声明。

### 类型断言 vs 泛型

# 声明文件

当使用第三方库时，需要引用声明文件才能获得对应的代码补全、接口提示灯功能

- `declare var`： 声明全局变量
- `declare function`： 声明全局方法
- `declare class`: 声明全局类
- `declare enum`： 声明全局枚举类型
- `declare namespace`： 声明（含有子属性的）全局对象
- `interface`和`type`： 声明全局类型
- `export`： 导出变量
- `export namespace`： 导出（含有子属性的）对象
- `export default`: ES6默认导出
- `export = `: commonjs导出模块
- `export as namespace`： UMD库声明全局变量
- `declare global`: 扩展全局对象
- `declare module`： 扩展模块
- ``/// <reference />`： 三斜线指令

## 什么是声明语句

使用jquery时，ts需要知道`jQuery`定义

```
declare var jQuery: (selector: string) => any;

jQuery('#foo')
```

## 什么是声明文件

通常我们会把声明语句放到一个单独的文件（`jQuery.d.ts`）中。这就是声明文件。声明文件必须以`.d.ts`为后缀，一般来说，ts会解析项目中所有的`*.ts`文件，当然也包含`.d.ts`结尾的文件。所以当我们将`jQuery.d.ts`放到项目中时，其他所有`*.ts`文件就可以获得`jQuery`的类型定义了。

```
// src/jQuery.d.ts

declare var jQuery: (selector: string) => any;

// src/index.ts
jQuery('#foo')
```

### 第三方声明文件

社区为我们提供了常用第三方库声明文件，通过`@types`统一管理第三方库的声明文件。

```
npm install @types/jquery --save-dev
```


# 内置对象

JavaScript有很多[内置对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)，可以直接在
TypeScript中使用

Node.js不是内置对象的一部分，如果想用TypeScript写Node.js，需要引入第三方声明文件

```
npm install @types/node --save-dev
```


https://ts.xcatliu.com/basics/built-in-objects.html

# 类型别名

类型别名用来给一个类型起一个新名字，类型别名常用于联合类型

```
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameSolver;
function getName(n: NameOrResolver): Name {
  if (typeof n === 'string') {
    return n
  } else {
    return n()
  }
}
```

# 字符串字面量类型

```
type EventNames = 'click' | 'scroll' | 'mousemove'
function handleEvent(ele: Element, event: EventNames) {}
```

# 参考资料
- [练习题答案](https://github.com/bcherny/programming-typescript-answers)
