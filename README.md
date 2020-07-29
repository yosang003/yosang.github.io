# 寒武纪TypeScript语言编程规范

|  版本  |    时间    |  作者  |  备注  |
|  :-----  |    :-----    |  :-----  |  :-----  |
| v1.0.0 | 2020-07-27 | 姚孝珍 | 初始版本 |

<!-- TOC -->

- [寒武纪TypeScript语言编程规范](#寒武纪typescript语言编程规范)
    - [声明文件编写规范](#声明文件编写规范)
        - [普通类型](#普通类型)
        - [泛型](#泛型)
        - [回调函数类型](#回调函数类型)
            - [回调函数返回值类型](#回调函数返回值类型)
            - [回调函数里的可选参数](#回调函数里的可选参数)
            - [重载与回调函数](#重载与回调函数)
        - [函数重载](#函数重载)
            - [顺序](#顺序)
            - [使用可选参数](#使用可选参数)
            - [使用联合类型](#使用联合类型)
    - [命名习惯 (Naming Conventions)](#命名习惯-naming-conventions)
        - [类](#类)
        - [接口](#接口)
        - [命名空间](#命名空间)
        - [枚举](#枚举)
    - [代码风格一致 (Code Style Consistency)](#代码风格一致-code-style-consistency)
        - [inerface声明顺序](#inerface声明顺序)
    - [参考](#参考)

<!-- /TOC -->

## 声明文件编写规范

### 普通类型

不要使用如下类型`Number`, `String`, `Boolean`或`Object`。
```typescript
// bad
function reverse(s: String): String;

// good
function reverse(s: string): string;
```

### 泛型

不要定义一个从来没使用过其类型参数的泛型类型。
```typescript
// bad
interface Something<T> {
  name: string;
}
let x: Something<number>;
let y: Something<string>;
// Expected error: Can't convert Something<number> to Something<string>!
x = y;
```

### 回调函数类型

#### 回调函数返回值类型

不要为返回值被忽略的回调函数设置一个 `any` 类型的返回值类型。应该给返回值被忽略的回调函数设置 `void` 类型的返回值类型。
```typescript
// bad
function fn(x: () => any) {
    x();
}

// good
function fn(x: () => void) {
    x();
}
```

#### 回调函数里的可选参数

尽量不要在回调函数里使用可选参数
```typescript
// bad
interface Fetcher {
    getObject(done: (data: any, elapsedTime?: number) => void): void;
}

// good
interface Fetcher {
    getObject(done: (data: any, elapsedTime: number) => void): void;
}
```

#### 重载与回调函数
不要因为回调函数参数个数不同而写不同的重载，应该只使用最大参数个数写一个重载
*为什么：回调函数总是可以忽略某个参数的，因此没必要为参数少的情况写重载*
```typescript
// bad
declare function beforeAll(action: () => void, timeout?: number): void;
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;

// good
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
```

### 函数重载

#### 顺序

不要把一般的重载放在精确的重载前面,应该排序重载令精确的排在一般的之前
*为什么：TypeScript会选择第一个匹配到的重载当解析函数调用的时候。 当前面的重载比后面的“普通”，那么后面的被隐藏了不会被调用*
```typescript
// bad
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: any, wat?

// good
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: string, :)
```

#### 使用可选参数

如果函数类型仅仅是末尾参数不同，则不需要使用重载，应尽可能使用可选参数代替
```typescript
// bad
interface Example {
    diff(one: string): number;
    diff(one: string, two: string): number;
    diff(one: string, two: string, three: boolean): number;
}

// good
interface Example {
    diff(one: string, two?: string, three?: boolean): number;
}
```

#### 使用联合类型

如果函数仅在某个位置上的参数类型不同，则不需要使用重载，应尽可能使用联合类型
```typescript
// bad
interface Moment {
    utcOffset(): number;
    utcOffset(b: number): Moment;
    utcOffset(b: string): Moment;
}

// good
interface Moment {
    utcOffset(): number;
    utcOffset(b: number|string): Moment;
}
```

## 命名习惯 (Naming Conventions)

### 类

使用帕斯卡(PascalCase)命名类名
```typescript
// bad
class foo { }

// good
class Foo { }
```
### 接口

* 使用帕斯卡(PascalCase)命名接口
* 使用驼峰(camelCase)命名成员
* 不要使用 `I` 前缀
```typescript
// bad 
interface IFoo { }

// good
interface Foo { }
```
### 命名空间

使用帕斯卡(PascalCase)命名
```typescript
// bad 
namespace foo { }

// good
namespace Foo { }
```
### 枚举

使用帕斯卡(PascalCase)命名枚举和枚举成员
```typescript
// bad 
enum direction {
    up,
    down,
    left,
    right,
}

// good
enum Direction {
    Up,
    Down,
    Left,
    Right,
}
```

## 代码风格一致 (Code Style Consistency)

### inerface声明顺序

日常用到比较多的是四种，只读参数放第一位，必选参数第二位，可选参数次之，不确定参数放最后
```typescript
// good
interface PersonProps {
  readonly x: number;
  readonly y: number;
  name: string;
  age: number;
  height?: number;
  [propName: string]: any;
}
```

## 参考

* [TypeScript声明文件规范](https://www.tslang.cn/docs/handbook/declaration-files/do-s-and-don-ts.html)
* [TypeScriptFAQ](https://github.com/Microsoft/TypeScript/wiki/FAQ)

