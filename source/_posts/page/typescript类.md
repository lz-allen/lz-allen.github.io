---
title: typescript类
categories: web前端
author: lz_allen
tags:
  - typescript
date: 2020-05-22 08:16:28
---

## 类

### 如何定义类

"strictPropertyInitialization": true / 启用类属性初始化的严格检查/
name!:string

```javascript
class Person{
    name:string;
    getName():void{
        console.log(this.name);
    }
}
let p1 = new Person();
p1.name = 'zhufeng';
p1.getName();
```

### 存取器

- 在 TypeScript 中，我们可以通过存取器来改变一个类中属性的读取和赋值行为
- 构造函数
- 主要用于初始化类的成员变量属性
- 类的对象创建时自动调用执行
- 没有返回值

```javascript
class User {
    myname:string;
    constructor(myname: string) {
        this.myname = myname;
    }
    get name() {
        return this.myname;
    }
    set name(value) {
        this.myname = value;
    }
}

let user = new User('zhufeng');
user.name = 'jiagou'; 
console.log(user.name); 
"use strict";
var User = /** @class */ (function () {
    function User(myname) {
        this.myname = myname;
    }
    Object.defineProperty(User.prototype, "name", {
        get: function () {
            return this.myname;
        },
        set: function (value) {
            this.myname = value;
        },
        enumerable: true,
        configurable: true
    });
    return User;
}());
var user = new User('zhufeng');
user.name = 'jiagou';
console.log(user.name);
```

### 参数属性

```javascript
class User {
    constructor(public myname: string) {}
    get name() {
        return this.myname;
    }
    set name(value) {
        this.myname = value;
    }
}

let user = new User('zhufeng');
console.log(user.name); 
user.name = 'jiagou'; 
console.log(user.name);
```

### readonly

- readonly修饰的变量只能在构造函数中初始化
- 在 TypeScript 中，const 是常量标志符，其值不能被重新分配
- TypeScript 的类型系统同样也允许将 interface、type、 class 上的属性标识为 readonly
- readonly 实际上只是在编译阶段进行代码检查。而 const 则会在运行时检查（在支持 const 语法的 JavaScript 运行时环境中）

```javascript
class Animal {
    public readonly name: string
    constructor(name:string) {
        this.name = name;
    }
    changeName(name:string){
        this.name = name;
    }
}

let a = new Animal('zhufeng');
a.changeName('jiagou');
```

### 继承

- 子类继承父类后子类的实例就拥有了父类中的属性和方法，可以增强代码的可复用性\
- 将子类公用的方法抽象出来放在父类中，自己的特殊逻辑放在子类中重写父类的逻辑
- super可以调用父类上的方法和属性

```javascript
class Person {
    name: string;//定义实例的属性，默认省略public修饰符
    age: number;
    constructor(name:string,age:number) {//构造函数
        this.name=name;
        this.age=age;
    }
    getName():string {
        return this.name;
    }
    setName(name:string): void{
        this.name=name;
    }
}
class Student extends Person{
    no: number;
    constructor(name:string,age:number,no:number) {
        super(name,age);
        this.no=no;
    }
    getNo():number {
        return this.no;
    }
}
let s1=new Student('zf',10,1);
console.log(s1);

```

### 类里面的修饰符

```javascript
class Father {
    public name: string;  //类里面 子类 其它任何地方外边都可以访问
    protected age: number; //类里面 子类 都可以访问,其它任何地方不能访问
    private money: number; //类里面可以访问， 子类和其它任何地方都不可以访问
    constructor(name:string,age:number,money:number) {//构造函数
        this.name=name;
        this.age=age;
        this.money=money;
    }
    getName():string {
        return this.name;
    }
    setName(name:string): void{
        this.name=name;
    }
}
class Child extends Father{
    constructor(name:string,age:number,money:number) {
        super(name,age,money);
    }
    desc() {
        console.log(`${this.name} ${this.age} ${this.money}`);
    }
}

let child = new Child('zf',10,1000);
console.log(child.name);
console.log(child.age);
console.log(child.money);
```

### 静态属性 静态方法

```javascript
class Father {
    static className='Father';
    static getClassName() {
        return Father.className;
    }
    public name: string;
    constructor(name:string) {//构造函数
        this.name=name;
    }

}
console.log(Father.className);
console.log(Father.getClassName());
```

### 装饰器

- 装饰器是一种特殊类型的声明，它能够被附加到类声明、方法、属性或参数上，可以修改类的行为
- 常见的装饰器有类装饰器、属性装饰器、方法装饰器和参数装饰器
- 装饰器的写法分为普通装饰器和装饰器工厂

#### 类装饰器

类装饰器在类声明之前声明，用来监视、修改或替换类定义

```javascript
namespace a {
    interface Person {
        name: string;
        eat: any
    }
    function enhancer(target: any) {
        target.prototype.name = 'zhufeng';
        target.prototype.eat = function () {
            console.log('eat');
        }
    }
    @enhancer
    class Person {
        constructor() { }
    }
    let p: Person = new Person();
    console.log(p.name);
    p.eat();
}

namespace b {
    interface Person {
        name: string;
        eat: any
    }
    function enhancer(name: string) {
        return function enhancer(target: any) {
            target.prototype.name = name;
            target.prototype.eat = function () {
                console.log('eat');
            }
        }
    }

    @enhancer('zhufeng')
    class Person {
        constructor() { }
    }
    let p: Person = new Person();
    console.log(p.name);
    p.eat();
}

namespace c {
    interface Person {
        name: string;
        eat: any
    }
    function enhancer(target: any) {
        return class {
            name: string = 'jiagou'
            eat() {
                console.log('吃饭饭');
            }
        }
    }
    @enhancer
    class Person {
        constructor() { }
    }
    let p: Person = new Person();
    console.log(p.name);
    p.eat();
}
```

#### 属性装饰器

- 属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数
- 属性装饰器用来装饰属性
- 第一个参数对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
- 第二个参数是属性的名称
- 方法装饰器用来装饰方法
- 第一个参数对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
- 第二个参数是方法的名称
- 第三个参数是方法描述符

```javascript
namespace d {
    function upperCase(target: any, propertyKey: string) {
        let value = target[propertyKey];
        const getter = function () {
            return value;
        }
        // 用来替换的setter
        const setter = function (newVal: string) {
            value = newVal.toUpperCase()
        };
        // 替换属性，先删除原先的属性，再重新定义属性
        if (delete target[propertyKey]) {
            Object.defineProperty(target, propertyKey, {
                get: getter,
                set: setter,
                enumerable: true,
                configurable: true
            });
        }
    }
    function noEnumerable(target: any, property: string, descriptor: PropertyDescriptor) {
        console.log('target.getName', target.getName);
        console.log('target.getAge', target.getAge);
        descriptor.enumerable = true;
    }
    function toNumber(target: any, methodName: string, descriptor: PropertyDescriptor) {
        let oldMethod = descriptor.value;
        descriptor.value = function (...args: any[]) {
            args = args.map(item => parseFloat(item));
            return oldMethod.apply(this, args);
        }
    }
    class Person {
        @upperCase
        name: string = 'zhufeng'
        public static age: number = 10
        constructor() { }
        @noEnumerable
        getName() {
            console.log(this.name);
        }
        @toNumber
        sum(...args: any[]) {
            return args.reduce((accu: number, item: number) => accu + item, 0);
        }
    }
    let p: Person = new Person();
    for (let attr in p) {
        console.log('attr=', attr);
    }
    p.name = 'jiagou';
    p.getName();
    console.log(p.sum("1", "2", "3"));
}
```

#### 参数装饰器

- 会在运行时当作函数被调用，可以使用参数装饰器为类的原型增加一些元数据
- 第1个参数对于静态成员是类的构造函数，对于实例成员是类的原型对象
- 第2个参数的名称
- 第3个参数在函数列表中的索引

```javascript
namespace d {
    interface Person {
        age: number;
    }
    function addAge(target: any, methodName: string, paramsIndex: number) {
        console.log(target);
        console.log(methodName);
        console.log(paramsIndex);
        target.age = 10;
    }
    class Person {
        login(username: string, @addAge password: string) {
            console.log(this.age, username, password);
        }
    }
    let p = new Person();
    p.login('zhufeng', '123456')
}
```

### 装饰器执行顺序

- 有多个参数装饰器时：从最后一个参数依次向前执行
- 方法和方法参数中参数装饰器先执行。
- 类装饰器总是最后执行
- 方法和属性装饰器，谁在前面谁先执行。因为参数属于方法一部分，所以参数会一直紧紧挨着方法执行

```javascript
namespace e {
    function Class1Decorator() {
        return function (target: any) {
            console.log("类1装饰器");
        }
    }
    function Class2Decorator() {
        return function (target: any) {
            console.log("类2装饰器");
        }
    }
    function MethodDecorator() {
        return function (target: any, methodName: string, descriptor: PropertyDescriptor) {
            console.log("方法装饰器");
        }
    }
    function Param1Decorator() {
        return function (target: any, methodName: string, paramIndex: number) {
            console.log("参数1装饰器");
        }
    }
    function Param2Decorator() {
        return function (target: any, methodName: string, paramIndex: number) {
            console.log("参数2装饰器");
        }
    }
    function PropertyDecorator(name: string) {
        return function (target: any, propertyName: string) {
            console.log(name + "属性装饰器");
        }
    }

    @Class1Decorator()
    @Class2Decorator()
    class Person {
        @PropertyDecorator('name')
        name: string = 'zhufeng';
        @PropertyDecorator('age')
        age: number = 10;
        @MethodDecorator()
        greet(@Param1Decorator() p1: string, @Param2Decorator() p2: string) { }
    }
}
```

/**
name属性装饰器
age属性装饰器
参数2装饰器
参数1装饰器
方法装饰器
类2装饰器
类1装饰器
 */

### 抽象类

- 抽象描述一种抽象的概念，无法被实例化，只能被继承
- 无法创建抽象类的实例\
- 抽象方法不能在抽象类中实现，只能在抽象类的具体子类中实现，而且必须实现

```javascript
abstract class Animal {
    name!:string;
    abstract speak():void;
}
class Cat extends Animal{
    speak(){
        console.log('喵喵喵');
    }
}
let animal = new Animal();//Cannot create an instance of an abstract class
animal.speak();
let cat = new Cat();
cat.speak();
```

- 访问控制修饰符 private protected public
- 只读属性 readonly
- 静态属性 static
- 抽象类、抽象方法 abstract
  
#### 抽象类 vs 接口

- 不同类之间公有的属性或方法，可以抽象成一个接口（Interfaces）
- 而抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
- 抽象类本质是一个无法被实例化的类，其中能够实现方法和初始化属性，而接口仅能够用于描述,既不提供方法的实现，也不为属性进行初始化
- 一个类可以继承一个类或抽象类，但可以实现（implements）多个接口
- 抽象类也可以实现接口

```javascript
abstract class Animal{
    name:string;
    constructor(name:string){
      this.name = name;
    }
    abstract speak():void;
  }
interface Flying{
      fly():void
}
class Duck extends Animal implements Flying{
      speak(){
          console.log('汪汪汪');
      }
      fly(){
          console.log('我会飞');
      }
}
let duck = new Duck('zhufeng');
duck.speak();
duck.fly();
```

#### 抽象方法

- 抽象类和方法不包含具体实现，必须在子类中实现
- 抽象方法只能出现在抽象类中
- 子类可以对抽象类进行不同的实现

```javascript
abstract class Animal{
    abstract speak():void;
}
class Dog extends  Animal{
    speak(){
        console.log('小狗汪汪汪');
    }
}
class Cat extends  Animal{
    speak(){
        console.log('小猫喵喵喵');
    }
}
let dog=new Dog();
let cat=new Cat();
dog.speak();
cat.speak();
```

### 重写(override) vs 重载(overload)

- 重写是指子类重写继承自父类中的方法
- 重载是指为同一个函数提供多个类型定义

```javascript
class Animal{
    speak(word:string):string{
        return '动作叫:'+word;
    }
}
class Cat extends Animal{
    speak(word:string):string{
        return '猫叫:'+word;
    }
}
let cat = new Cat();
console.log(cat.speak('hello'));
//--------------------------------------------
function double(val:number):number
function double(val:string):string
function double(val:any):any{
  if(typeof val == 'number'){
    return val *2;
  }
  return val + val;
}

let r = double(1);
console.log(r);
```

### 继承 vs 多态

- 继承(Inheritance)子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
- 多态(Polymorphism)由继承而产生了相关的不同的类，对同一个方法可以有不同的行为
  
```javascript
class Animal{
    speak(word:string):string{
        return 'Animal: '+word;
    }
}
class Cat extends Animal{
    speak(word:string):string{
        return 'Cat:'+word;
    }
}
class Dog extends Animal{
    speak(word:string):string{
        return 'Dog:'+word;
    }
}
let cat = new Cat();
console.log(cat.speak('hello'));
let dog = new Dog();
console.log(dog.speak('hello'));
```
