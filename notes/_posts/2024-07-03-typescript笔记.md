---
title: typescript笔记
---


# 一、 基础类型

## 1.1 boolean

```
let isDone: boolean = false;
```

## 1.2 number

```
let count: number = 10;
```

## 1.3 string

```
let name: string = 'hello';
```

## 1.4 Array

```
let list: number[] = [1, 2, 3];

let list: Array<number> = [1, 2, 3];  // Array<number>泛型语法
```

## 1.5 Enum

使用枚举可以定义一些带名字的常量。TypeScript支持数字和字符串的枚举。

1. 数字枚举
   ```
   enum Direction {
    NORTH,  // NORTH = 3,
    SOUTH,
    EAST,
    WEST,
   }
   let dir: Direction = Direction.NORTH;
   ```
   默认第一个元素值为0，也可以指定值，后续元素依次递增。
2. 字符串枚举
   ```
   enum Direction {
    NORTH = "NORTH",
    SOUTH = "SOUTH",
    EAST = "EAST",
    WEST = "WEST",
   }
   在字符串枚举里，每个成员都必须用字符串字面量设置取值。
   ```
3. 异构枚举
   ```
   enum Enum {
    A,    // 0
    B,    // 1
    C = "C",
    D = "D",
    E = 8,  // 8
    F,      // 9
   }
   ```

## 1.6 any

在TypeScript中，任何类型都可以被归位any类型，这使any类型成为了类型系统的顶级类型（也被称作全局超级类型）。

```
let notSure: any = 666;
notSure = 'hello';
notSure = false
```

TypeScript允许我们无须执行任何检测而对`any`类型的变量执行任何操作，比如：
```
let value: any

value.foo.bar   // ok
value.trim()    // ok
value()         // ok
new value()     // ok
value[0][1]     // ok
```

## 1.7 unknown

就像所有类型都可以赋值给`any`，所有类型都可以赋值给`unknown`。这使得`unknown`成为TypeScript的另一种顶级类型。
```
let value: unknown

value = true    // ok
value = 42      // ok
value = 'hell'  // ok
value = []      // ok
value = {}      // ok
value = null    // ok
```
任何类型数据都可以赋值给`unknown`变量。但是不能将`unknown`类型数据赋值给其他类型变量：
```
let value: unknown

let value1: unknown = value   // ok
let value2: any = value       // ok
let value3: boolean = value   // error
let value4: number = value    // error
let value5: string = value    // error
let value6: Object = value    // error
```
`unknown`类型只能被赋值给`any`和`unknown`本身。`unknown`类型值不能执行任何操作：
```
let value: unknown

value.foo.bar   // error
```

## 1.8 Tuple
支持多种类型元素的数组
```
let tupleType: [string, boolean]
tupleType = ['hello', true]
```

## 1.9 void
`void`表示没有任何类型，当一个函数没有返回值时，返回值类型为`void`

```
function warnUser(): void {
  console.log('this is a warning message')
}
```

声明一个`void`类型的变量没有什么作用，因为它的值只能是`undefined`或者`null`
```
let unusable: void = undefined
```

## 1.10 null和undefined
`undefined`和`null`各自类型分别为`undefined`和`null`
```
let u: undefined = undefined
let n: null = null
```
默认情况下`null`和`undefined`是所有类型的子类型。可以赋值给任意类型，但是如果设置了`--strictNullChecks`标记，只能复制给void和它们各自的类型。

## 1.11 never
`never`表示用不存在的值的类型，例如总是会抛出异常或根本不会有返回值的函数表达式或箭头表达式的返回值？？（没理解清楚）

```
// 返回never的函数必须存在无法到达的终点
function error(message: string): never {
  throw new Error(message)
}
function infiniteLoop(): never {
  while(true) {}
}
```

# 二、 TypeScript断言

类型断言可以告诉编译器，按某种类型处理数据。类型断言类似其他语言的类型转换，但是不进行特殊的数据检查和解构，只在编译阶段起作用。

## 2.1 “尖括号”语法

```
let someValue: any = 'this is a string'
let strLength: number = (<string>someValue).length
```

## 2.2 as语法

```
let someValue: any = 'this is a string'
let strLength: number = (someValue as string).length
```

# 三、 类型守卫(type guard)

> A type guard is some expression that performs a runtime check that guarantees the type in some scope.

类型守卫是可执行运行时检查的一种表达式，用于确保该类型在一定范围内。

## 3.1 in 关键字

```
interface Admin {
  name: string;
  privileges: string[];
}
interface Employee {
  name: string;
  startDate: Date;
}
type UnknownEmployee = Employee | Admin

function printEmployeeInformation(emp: UnknownEmployee) {
  if ('privileges' in emp) {
    console.log(emp.privileges)
  }
  if ('startDate' in emp) {
    console.log(emp.startDate)
  }
}
```

## 3.2 typeof 关键字

```
function padLeft(value: string, padding: string | number) {
  if (typeof padding === 'number') {
    return Array(padding + 1).join(' ') + value
  }
  if (typeof padding === 'string') {
    return padding + value
  }
}
```
`typeof`类型保护只支持两种形式：`typeof v === 'typename'`和`typeof v !== 'typename'`，typename必须是`number`,`string`,`boolean`或者`symbol`

## 3.3 instanceof 关键字

```
interface Padder {
  getPaddingString(): string;
}

class SpaceRepeatingPadder implements Padder {
  constructor(private numSpaces: number) {}
  getPaddingString() {
    return Array(this.numSpaces + 1).join(' ')
  }
}

class StringPadder implements Padder {
  constructor(private value: string) {}
  getPaddingString() {
    return this.value;
  }
}

let padder: Padder = new SapceRepeatingPadder(6)

if (padder instanceof SpaceRepeatingPadder) {}
```

# 四、 联合类型和类型别名

## 4.1 联合类型

联合类型通常与`null`或`undefined`一起使用

```
const sayHello = (name: string | undefined) => {}
```

这里`name`的类型是`string | undefined`表示可以将`string`或`undefined`的值传递给函数。

## 4.2 类型别名

```
type Message = string | string[];
let greet = (message: Message) => {}
```

# 五、 交叉类型

交叉类型是将多个类型合并为一个类型，让把多种类型叠加到一起成为一种新类型，它包含了所有类型的特性。

```
interface IPerson {
  id: string;
  age: number;
}
interface IWorker {
  companeyId: string;
}
type IStaff = IPerson & IWork

const staff: IStaff = {
  id: 'E1001',
  age: 33,
  companyId: 'EFT',
}
```

# 六、 函数

## 类型

```
let IdGenerator: (chars: string, nums: number) => string

function createUserId(name: string, id: number): string {
  return name + id
}
IdGenerator = createUserId
```

## 可选参数及默认参数

```
function createUserId(name: string, id: number, age?: number): string {
  return name + id
}

function createUserId(
  name: string = 'tom',
  id: number,
  age?: number
): string {
  return name + id
}
```

## 剩余参数

```
function push(array, ...items) {
  items.forEach(fucntion (item) {
    array.push(item)
  })
}

let a = []
push(a, 1, 2, 3)
```

## 函数重载
函数重载或方法重载是使用相同名称和不同参数数量或类型创建多个方法的一种能力。为同一个函数提供多个函数类型定义进行函数重载，编译器根据列表处理函数调用。

```
function add(a: number, b: number): number
function add(a: string, b: string): string
function add(a: string, b: number): string
function add(a: number, b: string): string
function add(a: Combinable, b: Combinable) {
  if (typeof a === 'string' || typeof b === 'string') {
    return a.toString() + b.toString()
  }
  return a + b
}
```

# 七、 数组

## 数组解构

```
let x: number
let y: number
let z: number
let fiveArray = [0, 1, 2, 3, 4]
[x, y, z] = fiveArray
```

## 数组展开运算符

```
let twoArray = [0, 1]
let fiveArray = [...twoArray, 2, 3, 4]
```

## 数组遍历

```
let colors: string[] = ['red', 'green', 'blue']
for (let i in colors) {
  console.log(i)
}
```

# 八、 对象

## 对象解构

```
let person = {
  name: 'tom',
  age: 33,
}
let { name, age } = person
```

## 对象展开运算符

```
let person = {
  name: 'tom',
  gender: 'male',
  address: 'beijing',
}
let personWithAge = { ...person, age: 33 }
let { name, ...rest } = person  // 获取除了某些项外的其他项
```

# 九、 接口

接口除了可用于对类的一部分行为进行抽象外，也常用于对对象的形状进行描述。

## 对象形状

```
interface Person {
  name: string;
  age: number;
}

let tom: Person = {
  name: 'tom',
  age: 33,
}
```

## 可选|只读属性

```
interface Person {
  readonly name: string;
  age?: number;
}
```

只读属性用于限制只能在对象创建时设置值。TypeScript提供了`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后不被修改。

```
let a: number[] = [1, 2, 3, 4]
let ro: ReadonlyArray<number> = a
ro[0] = 12    // error
ro.push(5)    // error
ro.length = 100   // error
```

# 十、 类

```
class Greeter {
  static cname: string = 'Greeter';
  greeting: string;
  constructor(message: string) {
    this.greeting = message
  }
  static getClassName() {
    return 'class name is Greeter'
  }
  greet() {
    return 'hello: ' + this.greeting
  }
}

let greeter = new Greeter('world')
```

# 参考资料
- [1.2W字 | 了不起的 TypeScript 入门教程](https://juejin.cn/post/6844904182843965453)