---
title: TypeScript基本数据类型
tags:
  - typescript
categories: web前端
date: 2019-05-17 15:19:26
---

## 数据类型

### 1.布尔类型(boolean)

```javascript

let isLzf: boolean=false;

```

### 1.2数字类型(number)

```javascript
let age: number=10;
```

### 1.3 字符串类型(string)

```javascript
let firstname: string='lzf';
```

### 1.4 数组类型(array)

```javascript
let arr2: number[]=[4,5,6];
let arr3: Array<number>=[7,8,9];
```

### 1.5 元组类型(tuple)

在 TypeScript 的基础类型中，元组（ Tuple ）表示一个已知数量和类型的数组

```javascript
let zf:[string,number] = ['zf',5];
zf[0].length;
zf[1].toFixed(2);
```

元组	数组
每一项可以是不同的类型	每一项都是同一种类型
有预定义的长度	没有长度限制
用于表示一个固定的结构	用于表示一个列表

```javascript
const animal:[string,number,boolean] = ['zhufeng',10,true];
```

### 1.6 枚举类型(enum)

事先考虑某一个变量的所有的可能的值，尽量用自然语言中的单词表示它的每一个值
比如性别、月份、星期、颜色、单位、学历

#### 1.6.1 普通枚举

```javascript
enum Gender{
    GIRL,
    BOY
}
console.log(`lzf是${Gender.BOY}`);
console.log(`zf${Gender.GIRL}`);

enum Week{
    MONDAY=1,
    TUESDAY=2
}
console.log(`今天是星期${Week.MONDAY}`);
```

#### 1.6.2 常数枚举

常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。
假如包含了计算成员，则会在编译阶段报错

```javascript
const enum Colors {
    Red,
    Yellow,
    Blue
}

let myColors = [Colors.Red, Colors.Yellow, Colors.Blue];
const enum Color {Red, Yellow, Blue = "blue".length};
```

### 1.7 任意类型(any)

any就是可以赋值给任意类型
第三方库没有提供类型文件时可以使用any
类型转换遇到困难时
数据结构太复杂难以定义

```javascript
let root:any=document.getElementById('root');
root.style.color='red';
let root:(HTMLElement|null)=document.getElementById('root');
root!.style.color='red';//非空断言操作符
```

### 1.8 null 和 undefined

null 和 undefined 是其它类型的子类型，可以赋值给其它类型，如数字类型，此时，赋值后的类型会变成 null 或 undefined
strictNullChecks 参数用于新的严格空检查模式,在严格空检查模式下， null 和 undefined 值都不属于任何一个类型，它们只能赋值给自己这种类型或者 any

```javascript
let x: number;
x = 1;
x = undefined;
x = null;

let y: number | null | undefined;
y = 1;
y = undefined;
y = null;
```

### 1.9 void 类型

void 表示没有任何类型
当一个函数没有返回值时，TS 会认为它的返回值是 void 类型。

```javascript
function greeting(name:string):void {
    console.log('hello',name);
    //当我们声明一个变量类型是 void 的时候，它的非严格模式(strictNullChecks:false)下仅可以被赋值为 null 和 undefined
    //严格模式(strictNullChecks:true)下只能返回undefined
    //return null;
    //return undefined;
}
```

### 1.10 never类型

never是其它类型(null undefined)的子类型，代表不会出现的值
作为不会返回（ return ）的函数的返回值类型

```javascript
// 返回never的函数 必须存在 无法达到（ unreachable ） 的终点
function error(message: string): never {
    throw new Error(message);
}
let result1 = error('hello');
// 由类型推论得到返回值为 never
function fail() {
    return error("Something failed");
}
let result = fail();

// 返回never的函数 必须存在 无法达到（ unreachable ） 的终点
function infiniteLoop(): never {
    while (true) {}
}
```

#### 1.10.1 strictNullChecks

在 TS 中， null 和 undefined 是任何类型的有效值，所以无法正确地检测它们是否被错误地使用。于是 TS 引入了 --strictNullChecks 这一种检查模式
由于引入了 --strictNullChecks ，在这一模式下，null 和 undefined 能被检测到。所以 TS 需要一种新的底部类型（ bottom type ）。所以就引入了 never。

```javascript
// Compiled with --strictNullChecks
function fn(x: number | string) {
  if (typeof x === 'number') {
    // x: number 类型
  } else if (typeof x === 'string') {
    // x: string 类型
  } else {
    // x: never 类型
    // --strictNullChecks 模式下，这里的代码将不会被执行，x 无法被观察
  }
}
```

#### 1.10.2 never 和 void 的区别、

void 可以被赋值为 null 和 undefined的类型。 never 则是一个不包含值的类型。
拥有 void 返回值类型的函数能正常运行。拥有 never 返回值类型的函数无法正常返回，无法终止，或会抛出异常。
1.11 类型推论
是指编程语言中能够自动推导出值的类型的能力，它是一些强静态类型语言中出现的特性
定义时未赋值就会推论成any类型
如果定义的时候就赋值就能利用到类型推论

```javascript
let username2;
username2 = 10;
username2 = 'zhufeng';
username2 = null;
```

### 1.12 包装对象（Wrapper Object）

JavaScript 的类型分为两种：原始数据类型（Primitive data types）和对象类型（Object types）。
所有的原始数据类型都没有属性（property）
原始数据类型
布尔值
数值
字符串

```javascript
null
undefined
Symbol
let name = 'zhufeng';
console.log(name.toUpperCase());
console.log((new String('zhufeng')).toUpperCase());
当调用基本数据类型方法的时候，JavaScript 会在原始数据类型和对象类型之间做一个迅速的强制性切换
let isOK: boolean = true; // 编译通过
let isOK: boolean = Boolean(1) // 编译通过
let isOK: boolean = new Boolean(1); // 编译失败   期望的 isOK 是一个原始数据类型
```

### 1.13 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种
未赋值时联合类型上只能访问两个类型共有的属性和方法

```javascript
let name: string | number;
console.log(name.toString());
name = 3;
console.log(name.toFixed(2));
name = 'zhufeng';
console.log(name.length);

export {};
```

### 1.14 类型断言

类型断言可以将一个联合类型的变量，指定为一个更加具体的类型
不能将联合类型断言为不存在的类型

```javascript
let name: string | number;
console.log((name as string).length);
console.log((name as number).toFixed(2));
console.log((name as boolean));
```

### 1.15 字面量类型

可以把字符串、数字、布尔值字面量组成一个联合类型

```javascript
type ZType = 1 | 'One'|true;
let t1:ZType = 1;
let t2:ZType = 'One';
let t3:ZType = true;
```

### 1.16 字符串字面量 vs 联合类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个, 联合类型（Union Types）表示取值可以为多种类型中的一种
字符串字面量 限定了使用该字面量的地方仅接受特定的值,联合类型 对于值并没有限定，仅仅限定值的类型需要保持一致
