---
title: typescript接口和泛型
categories: web前端
author: lz_allen
tags:
  - typescript
date: 2019-06-03 20:33:24
---

## 接口

- 接口一方面可以在面向对象编程中表示为行为的抽象，另外可以用来描述对象的形状
- 接口就是把一些类中共有的属性和方法抽象出来,可以用来约束实现此接口的类
- 一个类可以继承另一个类并实现多个接口
接口像插件一样是用来增强类的，而抽象类是具体类的抽象概念
一个类可以实现多个接口，一个接口也可以被多个类实现，但一个类的可以有多个子类，但只能有一个父类

### interface中可以用分号或者逗号分割每一项，也可以什么都不加

### 对象的形状

```typescript
//接口可以用来描述`对象的形状`,少属性或者多属性都会报错
interface Speakable{
    speak():void;
    name?:string;//？表示可选属性
}

let speakman:Speakable = {
    speak(){},//少属性会报错
    name,
    age//多属性也会报错
}
```

### 行为的抽象

```typescript
//接口可以在面向对象编程中表示为行为的抽象
interface Speakable{
    speak():void;
}
interface Eatable{
    eat():void
}
//一个类可以实现多个接口
class Person implements Speakable,Eatable{
    speak(){
        console.log('Person说话');
    }
    eat(){}
}
class TangDuck implements Speakable{
    speak(){
        console.log('TangDuck说话');
    }
    eat(){}
}
```

### 任意属性

```typescript
//无法预先知道有哪些新的属性的时候,可以使用 `[propName:string]:any`,propName名字是任意的
interface Person {
  readonly id: number;
  name: string;
  [propName: string]: any;
}

let p1 = {
  id:1,
  name:'zf',
  age:10
}
```

### 接口的继承

一个接口可以继承自另外一个接口

```typescript
interface Speakable {
    speak(): void
}
interface SpeakChinese extends Speakable {
    speakChinese(): void
}
class Person implements SpeakChinese {
    speak() {
        console.log('Person')
    }
    speakChinese() {
        console.log('speakChinese')
    }
}
```

#### readonly

```typescript
用 readonly 定义只读属性可以避免由于多人协作或者项目较为复杂等因素造成对象的值被重写
interface Person{
  readonly id:number;
  name:string
}
let tom:Person = {
  id :1,
  name:'zf'
}
tom.id = 1;
```

### 函数类型接口

```typescript
对方法传入的参数和返回值进行约束
interface discount{
  (price:number):number
}
let cost:discount = function(price:number):number{
   return price * .8;
}
```

### 可索引接口

```typescript
对数组和对象进行约束
userInterface 表示index的类型是 number，那么值的类型必须是 string
UserInterface2 表示：index 的类型是 string，那么值的类型必须是 string
interface UserInterface {
  [index:number]:string
}
let arr:UserInterface = ['zf1','zf2'];
console.log(arr);

interface UserInterface2 {
  [index:string]:string
}
let obj:UserInterface2 = {name:'zf'};
```

### 类接口

```typescript
对类的约束
interface Speakable {
    name: string;
    speak(words: string): void
}
class Dog implements Speakable {
    name!: string;
    speak(words:string) {
        console.log(words);
    }
}
let dog = new Dog();
dog.speak('汪汪汪');
```

### 构造函数的类型

```typescript
在 TypeScript 中，我们可以用 interface 来描述类
同时也可以使用interface里特殊的new()关键字来描述类的构造函数类型
class Animal{
  constructor(public name:string){
  }
}
interface WithNameClass{
  new(name:string):Animal
}
function createAnimal(clazz:WithNameClass,name:string){
   return new clazz(name);
}
let a = createAnimal(Animal,'zf');
console.log(a.name);
```

## 泛型

- 泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性
- 泛型T作用域只限于函数内部使用

### 泛型函数

```typescript
首先，我们来实现一个函数 createArray，它可以创建一个指定长度的数组，同时将每一项都填充一个默认值
function createArray(length: number, value: any): Array<any> {
  let result: any = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
let result = createArray(3,'x');
console.log(result);
使用了泛型

function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
      result[i] = value;
    }
    return result;
  }
let result = createArray2<string>(3,'x');
console.log(result);
```

### 类数组

```typescript
类数组（Array-like Object）不是数组类型，比如 arguments
function sum() {
    let args: IArguments = arguments;
    for (let i = 0; i < args.length; i++) {
        console.log(args[i]);
    }
}
sum(1, 2, 3);

let root = document.getElementById('root');
let children: HTMLCollection = (root as HTMLElement).children;
children.length;
let nodeList: NodeList = (root as HTMLElement).childNodes;
nodeList.length;
```

### 泛型类

```typescript
class MyArray<T>{
    private list:T[]=[];
    add(value:T) {
        this.list.push(value);
    }
    getMax():T {
        let result=this.list[0];
        for (let i=0;i<this.list.length;i++){
            if (this.list[i]>result) {
                result=this.list[i];
            }
        }
        return result;
    }
}
let arr=new MyArray();
arr.add(1); arr.add(2); arr.add(3);
let ret = arr.getMax();
console.log(ret);
```

### 泛型接口

泛型接口可以用来约束函数

```typescript
interface Calculate{
  <T>(a:T,b:T):T
}
let add:Calculate = function<T>(a:T,b:T){
  return a;
}
add<number>(1,2);
```

### 多个类型参数

```typescript
泛型可以有多个
function swap<A,B>(tuple:[A,B]):[B,A]{
  return [tuple[1],tuple[0]];
}
let swapped = swap<string,number>(['a',1]);
console.log(swapped);
console.log(swapped[0].toFixed(2));
console.log(swapped[1].length);
7.7 默认泛型类型
function createArray3<T=number>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
let result2 = createArray3(3,'x');
console.log(result2);
```

### 泛型约束

在函数中使用泛型的时候，由于预先并不知道泛型的类型，所以不能随意访问相应类型的属性或方法。

```typescript
function logger<T>(val: T) {
    console.log(val.length); //直接访问会报错
}
//可以让泛型继承一个接口
interface LengthWise {
    length: number
}
//可以让泛型继承一个接口
function logger2<T extends LengthWise>(val: T) {
    console.log(val.length)
}
logger2('zf');
logger2(1);
```

### 泛型接口定义

```typescript
定义接口的时候也可以指定泛型
interface Cart<T>{
  list:T[]
}
let cart:Cart<{name:string,price:number}> = {
  list:[{name:'zf',price:10}]
}
console.log(cart.list[0].name,cart.list[0].price);
```

### 泛型类型别名

泛型类型别名可以表达更复杂的类型

```typescript
type Cart<T> = {list:T[]} | T[];
let c1:Cart<string> = {list:['1']};
let c2:Cart<number> = [1];
```

### 泛型接口 vs 泛型类型别名

- 接口创建了一个新的名字，它可以在其他任意地方被调用。而类型别名并不创建新的名字，例如报错信息就不会使用别名
- 类型别名不能被 extends和 implements,这时我们应该尽量使用接口代替类型别名
- 当我们需要使用联合类型或者元组类型的时候，类型别名会更合适
