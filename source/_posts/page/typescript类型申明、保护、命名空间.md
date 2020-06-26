---
title: typescript类型申明、保护、命名空间
categories: web前端
author: lz_allen
tags:
  - typescript
date: 2020-05-26 20:50:49
---

## 结构类型系统

### 接口的兼容性

```typescript
- 如果传入的变量和声明的类型不匹配，TS就会进行兼容性检查
- 原理是Duck-Check,就是说只要目标类型中声明的属性变量在源类型中都存在就是兼容的

interface Animal {
    name: string;
    age: number;
}

interface Person {
    name: string;
    age: number;
    gender: number
}
// 要判断目标类型`Person`是否能够兼容输入的源类型`Animal`
function getName(animal: Animal): string {
    return animal.name;
}

let p = {
    name: 'zf',
    age: 10,
    gender: 0
}

getName(p);
//只有在传参的时候两个变量之间才会进行兼容性的比较，赋值的时候并不会比较,会直接报错
let a: Animal = {
    name: 'zf',
    age: 10,
    gender: 0
}
```

### 基本类型的兼容性

```typescript
//基本数据类型也有兼容性判断
let num : string|number;
let str:string='zf';
num = str;

//只要有toString()方法就可以赋给字符串变量
let num2 : {
  toString():string
}

let str2:string='jiagou';
num2 = str2;
```

### 类的兼容性

在TS中是结构类型系统，只会对比结构而不在意类型

```typescript
class Animal{
    name:string
}
class Bird extends Animal{
   swing:number
}

let a:Animal;
a = new Bird();

let b:Bird;
//并不是父类兼容子类，子类不兼容父类
b = new Animal();

class Animal{
  name:string
}
//如果父类和子类结构一样，也可以的
class Bird extends Animal{}

let a:Animal;
a = new Bird();

let b:Bird;
b = new Animal();

//甚至没有关系的两个类的实例也是可以的
class Animal{
  name:string
}
class Bird{
  name:string
}
let a:Animal ;
a = new Bird();
let b:Bird;
b = new Animal();
```

### 函数的兼容性

比较函数的时候是要先比较函数的参数，再比较函数的返回值

#### 比较参数

```typescript
type sumFunc = (a:number,b:number)=>number;
let sum:sumFunc;
function f1(a:number,b:number):number{
  return a+b;
}
sum = f1;

//可以省略一个参数
function f2(a:number):number{
   return a;
}
sum = f2;

//可以省略二个参数
function f3():number{
    return 0;
}
sum = f3;

 //多一个参数可不行
function f4(a:number,b:number,c:number){
    return a+b+c;
}
sum = f4;
```

#### 比较返回值

```typescript
type GetPerson = ()=>{name:string,age:number};
let getPerson:GetPerson;
//返回值一样可以
function g1(){
    return {name:'zf',age:10};
}
getPerson = g1;
//返回值多一个属性也可以
function g2(){
    return {name:'zf',age:10,gender:'male'};
}
getPerson = g2;
//返回值少一个属性可不行
function g3(){
    return {name:'zf'};
}
getPerson = g3;
//因为有可能要调用返回值上的方法
getPerson().age.toFixed();
```

### 函数参数的协变

当比较函数参数类型时，只有当源函数参数能够赋值给目标函数或者反过来时才能赋值成功

```TYPESCRIPT
"strictFunctionTypes": false
let sourceFunc = (args: number | string) => { }
let target1Func = (args: number | string) => { }
let target2Func = (args: number | string | boolean) => { }
sourceFunc = target1Func;
sourceFunc = target2Func;

interface Event {
    timestamp: number;
}

interface MouseEvent extends Event {
    eventX: number;
    eventY: number;
}

interface KeyEvent extends Event {
    keyCode: number;
}

function addEventListener(eventType: EventType, handler: (n: Event) => void) { }

addEventListener(EventType.Mouse, (e: MouseEvent) => console.log(e.eventX + ', ' + e.eventY));
addEventListener(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.eventX + ', ' + e.eventY)));
```

### 泛型的兼容性

泛型在判断兼容性的时候会先判断具体的类型,然后再进行兼容性判断

1. 接口内容为空没用到泛型的时候是可以的

```typescript
interface Empty<T>{}
let x!:Empty<string>;
let y!:Empty<number>;
x = y;
```

2. 接口内容不为空的时候不可以

```typescript
interface NotEmpty<T>{
  data:T
}
let x1!:NotEmpty<string>;
let y1!:NotEmpty<number>;
x1 = y1;

//实现原理如下,称判断具体的类型再判断兼容性
interface NotEmptyString{
    data:string
}

interface NotEmptyNumber{
    data:number
}
let xx2!:NotEmptyString;
let yy2!:NotEmptyNumber;
xx2 = yy2;
```

### 枚举的兼容性

- 枚举类型与数字类型兼容，并且数字类型与枚举类型兼容
- 不同枚举类型之间是不兼容的

//数字可以赋给枚举

```typescript
enum Colors {Red,Yellow}
let c:Colors;
c = Colors.Red;
c = 1;
c = '1';

//枚举值可以赋给数字
let n:number;
n = 1;
n = Colors.Red;
```

## 类型保护

- 类型保护就是一些表达式，他们在编译的时候就能通过类型信息确保某个作用域内变量的类型
- 类型保护就是能够通过关键字判断出分支中的类型

### typeof 类型保护

```typescript
function double(input: string | number | boolean) {
    if (typeof input === 'string') {
        return input + input;
    } else {
        if (typeof input === 'number') {
            return input * 2;
        } else {
            return !input;
        }
    }
}
```

### instanceof类型保护

```typescript
class Animal {
    name!: string;
}
class Bird extends Animal {
    swing!: number
}
function getName(animal: Animal) {
    if (animal instanceof Bird) {
        console.log(animal.swing);
    } else {
        console.log(animal.name);
    }
}
```

### null保护

如果开启了strictNullChecks选项，那么对于可能为null的变量不能调用它上面的方法和属性

```typescript
function getFirstLetter(s: string | null) {
    //第一种方式是加上null判断
    if (s == null) {
        return '';
    }
    //第二种处理是增加一个或的处理
    s = s || '';
    return s.charAt(0);
}
//它并不能处理一些复杂的判断，需要加非空断言操作符
function getFirstLetter2(s: string | null) {
    function log() {
        console.log(s!.trim());
    }
    s = s || '';
    log();
    return s.charAt(0);
}
```

### 链判断运算符

- 链判断运算符是一种先检查属性是否存在，再尝试访问该属性的运算符，其符号为 ?.
- 如果运算符左侧的操作数 ?. 计算为 undefined 或 null，则表达式求值为 undefined 。否则，正常触发目标属性访问，方法或函数调用。
- 链判断运算符 还处于 stage1 阶段,TS 也暂时不支持

```typescript
a?.b; //如果a是null/undefined,那么返回undefined，否则返回a.b的值.
a == null ? undefined : a.b;

a?.[x]; //如果a是null/undefined,那么返回undefined，否则返回a[x]的值
a == null ? undefined : a[x];

a?.b(); // 如果a是null/undefined,那么返回undefined
a == null ? undefined : a.b(); //如果a.b不函数的话抛类型错误异常,否则计算a.b()的结果

a?.(); //如果a是null/undefined,那么返回undefined
a == null ? undefined : a(); //如果A不是函数会抛出类型错误
//否则 调用a这个函数
```

### 可辨识的联合类型

- 就是利用联合类型中的共有字段进行类型保护的一种技巧
- 相同字段的不同取值就是可辨识

```typescript
interface WarningButton{
  class:'warning',
  text1:'修改'
}
interface DangerButton{
  class:'danger',
  text2:'删除'
}
type Button = WarningButton|DangerButton;
function getButton(button:Button){
 if(button.class=='warning'){
  console.log(button.text1);
 }
 if(button.class=='danger'){
  console.log(button.text2);
 }
}
```

### in操作符

in 运算符可以被用于参数类型的判断

```typescript
interface Bird {
    swing: number;
}

interface Dog {
    leg: number;
}

function getNumber(x: Bird | Dog) {
    if ("swing" in x) {
      return x.swing;
    }
    return x.leg;
}
```

### 自定义的类型保护

- TypeScript 里的类型保护本质上就是一些表达式，它们会在运行时检查类型信息，以确保在某个作用域里的类型是符合预期的
- type is Type1Class就是类型谓词
- 谓词为 parameterName is Type这种形式,parameterName必须是来自于当前函数签名里的一个参数名
- 每当使用一些变量调用isType1时，如果原始类型兼容，TypeScript会将该变量缩小到该特定类型

```typescript
function isType1(type: Type1Class | Type2Class): type is Type1Class {
    return (<Type1Class>type).func1 !== undefined;
}
interface Bird {
  swing: number;
}

interface Dog {
  leg: number;
}

//没有相同字段可以定义一个类型保护函数
function isBird(x:Bird|Dog): x is Bird{
  return (<Bird>x).swing == 2;
  //return (x as Bird).swing == 2;
}

function getAnimal(x: Bird | Dog) {
  if (isBird(x)) {
    return x.swing;
  }
  return x.leg;
}
```

### unknown

- TypeScript 3.0 引入了新的unknown 类型，它是 any 类型对应的安全类型
- unknown 和 any 的主要区别是 unknown 类型会更加严格：在对 unknown 类型的值执行大多数操作之前，我们必须进行某种形式的检查。而在对 any 类型的值执行操作之前，我们不必进行任何检查

#### any 类型

- 在 TypeScript 中，任何类型都可以被归为 any 类型。这让 any 类型成为了类型系统的 顶级类型 (也被称作 全局超级类型)。
- TypeScript允许我们对 any 类型的值执行任何操作，而无需事先执行任何形式的检查

```typescript
let value: any;

value = true;             // OK
value = 42;               // OK
value = "Hello World";    // OK
value = [];               // OK
value = {};               // OK
value = Math.random;      // OK
value = null;             // OK
value = undefined;        // OK


let value: any;
value.foo.bar;  // OK
value.trim();   // OK
value();        // OK
new value();    // OK
```

#### unknown 类型

- 就像所有类型都可以被归为 any，所有类型也都可以被归为 unknown。这使得 unknown 成为 TypeScript 类型系统的另一种顶级类型（另一种是 any）
- 任何类型都可以赋值给unknown类型
- `unknown`类型只能被赋值给`any`类型和`unknown`类型本身

```typescript
let value: unknown;
value = true; // OK
value = 42; // OK
value = "Hello World"; // OK
value = []; // OK
value = {}; // OK
value = Math.random; // OK
value = null; // OK
value = undefined; // OK
value = new TypeError(); // OK
```

```typescript
let value: unknown;

let value1: unknown = value;   // OK
let value2: any = value;       // OK
let value3: boolean = value;   // Error
let value4: number = value;    // Error
let value5: string = value;    // Error
let value6: object = value;    // Error
let value7: any[] = value;     // Error
let value8: Function = value;  // Error
```

### 缩小 unknown 类型范围

- 如果没有类型断言或类型细化时，不能在unknown上面进行任何操作
- typeof
- instanceof
- 自定义类型保护函数
- 可以对 unknown 类型使用类型断言

```typescript
const value: unknown = "Hello World";
const someString: string = value as string;
```

### 联合类型中的 unknown 类型

在联合类型中，unknown 类型会吸收任何类型。这就意味着如果任一组成类型是 unknown，联合类型也会相当于 unknown：

```typescript
type UnionType1 = unknown | null;       // unknown
type UnionType2 = unknown | undefined;  // unknown
type UnionType3 = unknown | string;     // unknown
type UnionType4 = unknown | number[];   // unknown
```

### 交叉类型中的 unknown 类型

在交叉类型中，任何类型都可以吸收 unknown 类型。这意味着将任何类型与 unknown 相交不会改变结果类型

```typescript
type IntersectionType1 = unknown & null;       // null
type IntersectionType2 = unknown & undefined;  // undefined
type IntersectionType3 = unknown & string;     // string
type IntersectionType4 = unknown & number[];   // number[]
type IntersectionType5 = unknown & any;        // any
// never是unknown的子类型
type isNever = never extends unknown ? true : false;
// keyof unknown 等于never
type key = keyof unknown;
// 只能对unknown进行等或不等操作，不能进行其它操作
un1===un2;
un1!==un2;
un1 += un2;
// 不能做任何操作
// 不能访问属性
// 不能作为函数调用
// 不能当作类的构造函数不能创建实例
un.name
un();
new un();
```

### 映射属性

- 如果映射类型遍历的时候是unknown,不会映射属性

```typescript
type getType<T> = {
  [P in keyof T]:number
}
type t = getType<unknown>;
```

## 类型变换

### 交叉类型

- 交叉类型（Intersection Types）表示将多个类型合并为一个类型
  
```typescript
interface Bird {
    name: string,
    fly(): void
}
interface Person {
    name: string,
    talk(): void
}
type BirdPerson = Bird & Person;
let p: BirdPerson = { name: 'zf', fly() { }, talk() { } };
p.fly;
p.name
p.talk;
```

### typeof

- 可以获取一个变量的类型
- 先定义类型，再定义变量

```typescript
type People = {
    name:string,
    age:number,
    gender:string
}
let p1:People = {
    name:'zf',
    age:10,
    gender:'male'
}
//先定义变量，再定义类型
let p1 = {
    name:'zf',
    age:10,
    gender:'male'
}
type People = typeof p1;
function getName(p:People):string{
    return p.name;
}
getName(p1);
```

### 索引访问操作符

- 可以通过[]获取一个类型的子类型

```typescript
interface Person{
    name:string;
    age:number;
    job:{
        name:string
    };
    interests:{name:string,level:number}[]
}
let FrontEndJob:Person['job'] = {
    name:'前端工程师'
}
let interestLevel:Person['interests'][0]['level'] = 2;
```

### keyof

- 索引类型查询操作符

```typescript
interface Person{
  name:string;
  age:number;
  gender:'male'|'female';
}
//type PersonKey = 'name'|'age'|'gender';
type PersonKey = keyof Person;

function getValueByKey(p:Person,key:PersonKey){
  return p[key];
}
let val = getValueByKey({name:'zf',age:10,gender:'male'},'name');
console.log(val);
```

### 映射类型

- 在定义的时候用in操作符去批量定义类型中的属性

```typescript
interface Person{
  name:string;
  age:number;
  gender:'male'|'female';
}
//批量把一个接口中的属性都变成可选的
type PartPerson = {
  [Key in keyof Person]?:Person[Key]
}

let p1:PartPerson={};
//也可以使用泛型
type Part<T> = {
  [key in keyof T]?:T[key]
}
let p2:Part<Person>={};
```

### 内置工具类型

- TS 中内置了一些工具类型来帮助我们更好地使用类型系统
  
符号| 含义
+?|变为可远
->|变为必选

#### Partial

- Partial 可以将传入的属性由非可选变为可选，具体使用如下：

```typescript
type Partial<T> = { [P in keyof T]?: T[P] };
interface A {
  a1: string;
  a2: number;
  a3: boolean;
}
type aPartial = Partial<A>;
const a: aPartial = {}; // 不会报错
```

#### Required

- Required 可以将传入的属性中的可选项变为必选项，这里用了 -? 修饰符来实现。

```typescript
//type Required<T> = { [P in keyof T]-?: T[P] };

interface Person{
  name:string;
  age:number;
  gender?:'male'|'female';
}
/**
 * type Require<T> = { [P in keyof T]-?: T[P] };
 */
let p:Required<Person> = {
  name:'zf',
  age:10,
  //gender:'male'
}
```

#### Readonly

- Readonly 通过为传入的属性每一项都加上 readonly 修饰符来实现。

```typescript
interface Person{
  name:string;
  age:number;
  gender?:'male'|'female';
}
//type Readonly<T> = { readonly [P in keyof T]: T[P] };
let p:Readonly<Person> = {
  name:'zf',
  age:10,
  gender:'male'
}
p.age = 11;
```

#### Pick

- Pick 能够帮助我们从传入的属性中摘取某一项返回

```typescript
interface Animal {
  name: string;
  age: number;
  gender:number
}
/**
 * From T pick a set of properties K
 * type Pick<T, K extends keyof T> = { [P in K]: T[P] };
 */
// 摘取 Animal 中的 name 属性
interface Person {
    name: string;
    age: number;
    married: boolean
}
function pick<T, K extends keyof T>(obj: T, keys: K[]): Pick<T, K> {
    const result: any = {};
    keys.map(key => {
        result[key] = obj[key];
    });
    return result
}
let person: Person = { name: 'zf', age: 10, married: true };
let result: Pick<Person, 'name' | 'age'> = pick<Person, 'name' | 'age'>(person, ['name', 'age']);
console.log(result);
```

#### Record

```typescript
function mapObject<K extends string | number, T, U>(obj: Record<K, T>, map: (x: T) => U): Record<K, U> {
    let result: any = {};
    for (const key in obj) {
        result[key] = map(obj[key]);
    }
    return result;
}
let names = { 0: 'hello', 1: 'world' };
let lengths = mapObject<string | number, string, number>(names, (s: string) => s.length);
console.log(lengths);//{ '0': 5, '1': 5 }
```

#### Proxy

```typescript
type Proxy<T> = {
    get(): T;
    set(value: T): void;
}
type Proxify<T> = {
    [P in keyof T]: Proxy<T[P]>
}
function proxify<T>(obj: T): Proxify<T> {
    let result = {} as Proxify<T>;
    for (const key in obj) {
        result[key] = {
            get: () => obj[key],
            set: (value) => obj[key] = value
        }
    }
    return result;
}
let props = {
    name: 'zf',
    age: 10
}
let proxyProps = proxify(props);
console.log(proxyProps);

function unProxify<T>(t: Proxify<T>): T {
    let result = {} as T;
    for (const k in t) {
        result[k] = t[k].get();
    }
    return result;
}

let originProps = unProxify(proxyProps);
console.log(originProps);
```

### 映射类型修饰符的控制

- TypeScript中增加了对映射类型修饰符的控制
- 具体而言，一个 readonly 或 ? 修饰符在一个映射类型里可以用前缀 + 或-来表示这个修饰符应该被添加或移除
- TS 中部分内置工具类型就利用了这个特性（Partial、Required、Readonly...），这里我们可以参考 Partial、Required 的实现

## 条件类型

- 在定义泛型的时候能够添加进逻辑分支，以后泛型更加灵活
utility-types

### 定义条件类型

```typescript
interface Fish {
    name: string
}
interface Water {
    name: string
}
interface Bird {
    name: string
}
interface Sky {
    name: string
}
//三元运算符
type Condition<T> = T extends Fish ? Water : Sky;
let condition: Condition<Fish> = { name: '水' };
```

### 条件类型的分发

```typescript
interface Fish {
    fish: string
}
interface Water {
    water: string
}
interface Bird {
    bird: string
}
interface Sky {
    sky: string
}

type Condition<T> = T extends Fish ? Water : Sky;
//(Fish extends Fish ? Water : Sky) | (Bird extends Fish ? Water : Sky)
// Water|Sky
let condition1: Condition<Fish | Bird> = { water: '水' };
let condition2: Condition<Fish | Bird> = { sky: '天空' };
```

### 内置条件类型

- TS 在内置了一些常用的条件类型，可以在 lib.es5.d.ts 中查看：
- infer最早出现在此 PR 中，表示在 extends 条件语句中待推断的类型变量

#### Exclude

- 从 T 可分配给的类型中排除 U `js type Exclude<T, U> = T extends U ? never : T;

```typescript
type E = Exclude<string|number,string>; let e:E = 10;
```

#### Extract<T, U>

- 从 T 可分配的类型中提取 U

```typescript
type Extract<T, U> = T extends U ? T : never;

type  E = Extract<string|number,string>;
let e:E = '1';
```

#### NonNullable

- 从 T 中排除 null 和 undefined

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;
type  E = NonNullable<string|number|null|undefined>;
let e:E = null;
```

#### ReturnType

- 获取函数类型的返回类型

```typescript
function getUserInfo() {
  return { name: "zf", age: 10 };
}

// 通过 ReturnType 将 getUserInfo 的返回值类型赋给了 UserInfo
type UserInfo = ReturnType<typeof getUserInfo>;

const userA: UserInfo = {
  name: "zf",
  age: 10
};

type ReturnType2<T extends (...args: any[]) => any> = T extends (...args: any[]) => infer R ? R : any;
type AnyFunction = (...args: any[]) => any;
type ReturnType2<T extends AnyFunction> = T extends (...args: any[]) => infer R ? R : any
```

#### Parameters

Constructs a tuple type of the types of the parameters of a function type T
Parameters

```typescrript
declare function f1(arg: { a: number, b: string }): void
type T0 = Parameters2<() => string>;  // []
type T1 = Parameters2<(s: string) => void>;  // [string]
type T2 = Parameters2<(<T>(arg: T) => T)>;  // [unknown]
type AnyFunction = (...args: any[]) => any;
//type ReturnType2<T extends AnyFunction> = T extends (...args: any[]) => infer R ? R : any;
type Parameters2<T extends AnyFunction> = T extends (...args: infer R) => any ? R : any;
```

#### InstanceType

- 获取构造函数类型的实例类型
- InstanceType

```typescript
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    getName() { console.log(this.name) }
}
type constructorParameters = ConstructorParameters<typeof Person>;
let params: constructorParameters = ['zf']
type Instance = InstanceType<typeof Person>;
let instance: Instance = { name: 'zf', getName() { } };
type Constructor = new (...args: any[]) => any;
type ConstructorParameters<T extends Constructor> = T extends new (...args: infer P) => any ? P : never;
type InstanceType<T extends Constructor> = T extends new (...args: any[]) => infer R ? R : any;
```

#### infer

- typescript_zh
- codesandbox

```typescript
type ElementType<T> = T extends any[] ? T[number] : T;
type e1 = ElementType<string[]>;
type e2 = ElementType<string>;
type GetElementType<T> = T extends Array<infer U> ? U : T;
type e3 = GetElementType<string[]>;
type e4 = GetElementType<string>;

interface Action<T> {
    payload?: T
    type: string
}

class EffectModule {
  count = 1;
  message = "hello!";
  delay(input: Promise<number>) {
      return input.then(i => ({
          payload: `hello ${i}!`,
          type: 'delay'
      });
  }
  setMessage(action: Action<Date>) {
    return {
        payload: action.payload!.getMilliseconds(),
        type: "set-message"
    };
  }
}

type FuncName<T> = {
    [P in keyof T]: T[P] extends Function ? P : never;
}[keyof T];
type ts = FuncName<EffectModule>;
type Connect = (module: EffectModule) => { [T in FuncName<EffectModule>]: EffectModule[T] }


type TransformResultType<RT> = {
    [s in keyof RT]:
    RT[s] extends (input: Promise<infer T>) => Promise<Action<infer U>> ?
    (input: T) => Action<U> : (
        RT[s] extends (action: Action<infer T>) => Action<infer U> ? (action: T) => Action<U> : never
    )
}
type connectedResultType = ReturnType<Connect>;
type result = TransformResultType<connectedResultType>
export { };
```

## 模块VS命名空间

### 模块

- 模块是TS中外部模块的简称，侧重于代码和复用
- 模块在期自身的作用域里执行，而不是在全局作用域里
- 一个模块里的变量、函数、类等在外部是不可见的，除非你把它导出
- 如果想要使用一个模块里导出的变量，则需要导入

```typescript
export const a = 1;
export const b = 2;
export default 'zf';
import name, { a, b } from './1';
console.log(name, a, b);
```

## 命名空间

- 在代码量较大的情况下，为了避免命名空间冲突，可以将相似的函数、类、接口放置到命名空间内
- 命名空间可以将代码包裹起来，只对外暴露需要在外部访问的对象，命名空间内通过export向外导出
- 命名空间是内部模块，主要用于组织代码，避免命名冲突

### 内部划分

```typescript
export namespace zoo {
    export class Dog { eat() { console.log('zoo dog'); } }
}
export namespace home {
    export class Dog { eat() { console.log('home dog'); } }
}
let dog_of_zoo = new zoo.Dog();
dog_of_zoo.eat();
let dog_of_home = new home.Dog();
dog_of_home.eat();
import { zoo } from './3';
let dog_of_zoo = new zoo.Dog();
dog_of_zoo.eat();
```

## 类型声明

- 声明文件可以让我们不需要将JS重构为TS，只需要加上声明文件就可以使用系统
- 类型声明在编译的时候都会被删除，不会影响真正的代码

### 普通类型声明

```typescript
declare const $: (selector: string) => { //变量
    click(): void;
    width(length: number): void;
};
$('#root').click();
console.log($('#root').width);
declare let name: string;  //变量
declare let age: number;  //变量
declare function getName(): string;  //方法
declare class Animal { name: string }  //类
console.log(name, age);
getName();
new Animal();
export default {};
```

### 外部枚举

- 外部枚举是使用declare enum定义的枚举类型
- 外部枚举用来描述已经存在的枚举类型的形状

```typescript
declare enum Seasons {
    Spring,
    Summer,
    Autumn,
    Winter
}

let seasons = [
    Seasons.Spring,
    Seasons.Summer,
    Seasons.Autumn,
    Seasons.Winter
];
declare 定义的类型只会用于编译时的检查，编译结果中会被删除。上例的编译结果如下

var seasons = [
    Seasons.Spring,
    Seasons.Summer,
    Seasons.Autumn,
    Seasons.Winter
];

// 也可以同时使用declare 和 const

declare const enum Seasons {
    Spring,
    Summer,
    Autumn,
    Winter
}

let seasons = [
    Seasons.Spring,
    Seasons.Summer,
    Seasons.Autumn,
    Seasons.Winter
];

//编译结果

var seasons = [
    0 /* Spring */,
    1 /* Summer */,
    2 /* Autumn */,
    3 /* Winter */
];
```

## namespace

- 如果一个全局变量包括了很多子属性，可能使用namespace
- 在声明文件中的namespace表示一个全局变量包含很多子属性
- 在命名空间内部不需要使用 declare 声明属性或方法

```typescript
declare namespace ${
    function ajax(url:string,settings:any):void;
    let name:string;
    namespace fn {
        function extend(object:any):void;
    }
}
$.ajax('/api/users',{});
$.fn.extend({
    log:function(message:any){
        console.log(message);
    }
});
export {};
```

### 类型声明文件

- 我们可以把类型声明放在一个单独的类型声明文件中
- 可以在类型声明文件中使用类型声明
- 文件命名规范为*.d.ts
- 观看类型声明文件有助于了解库的使用方式

#### jquery.d.ts

typings\jquery.d.ts

```typescript
declare const $:(selector:string)=>{
    click():void;
    width(length:number):void;
}
```

### tsconfig.json

tsconfig.json

```typescript
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "ES2015",  
    "outDir":"lib"
  },
  "include": [
    "src/**/*",
    "typings/**/*"
  ]
}
```

#### test.js

src\test.ts

```typescript
$('#button').click();
$('#button').width(100);
export {};
```

## 第三方声明文件

- 可以安装使用第三方的声明文件
- @types是一个约定的前缀，所有的第三方声明的类型库都会带有这样的前缀
- JavaScript 中有很多内置对象，它们可以在 TypeScript 中被当做声明好了的类型
- 内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准
- 这些内置对象的类型声明文件，就包含在TypeScript 核心库的类型声明文件中

### 使用jquery

cnpm i jquery -S
对于common.js风格的模块必须使用 import * as 
import * as jQuery from 'jquery';
jQuery.ajax('/user/1');

### 安装声明文件

cnpm i @types/jquery -S

### 自己编写声明文件

- 模块查找规则
- `node_modules\@types\jquery/index.d.ts
- 我们可以自己编写声明文件并配置tsconfig.json

```typescript
// index.d.ts
// types\jquery\index.d.ts

declare function jQuery(selector:string):HTMLElement;
declare namespace jQuery{
  function ajax(url:string):void
}
export default jQuery;
```
 
### tsconfig.json

- 如果配置了paths,那么在引入包的的时候会自动去paths目录里找类型声明文件
- 在 tsconfig.json 中，我们通过 compilerOptions 里的 paths 属性来配置路径映射
- paths是模块名到基于baseUrl的路径映射的列表

```typescript
{
"baseUrl": "./",// 使用 paths 属性的话必须要指定 baseUrl 的值
"paths": {
"*":["types/*"]
}
```

### npm声明文件可能的位置

node_modules/jquery/package.json
"types":"types/xxx.d.ts"
node_modules/jquery/index.d.ts
node_modules/@types/jquery/index.d.ts

### 扩展全局变量的类型

#### 扩展局部变量类型

```typescript
declare var String: StringConstructor;
interface StringConstructor {
    new(value?: any): String;
    (value?: any): string;
    readonly prototype: String;
}
interface String {
    toString(): string;
}
//扩展类的原型
interface String {
    double():string;
}

String.prototype.double = function(){
    return this+'+'+this;
}
console.log('hello'.double());

//扩展类的实例
interface Window{
    myname:string
}
console.log(window.myname);
//export {} 没有导出就是全局扩展
```

### 模块内全局扩展

types\global\index.d.ts

```typescript
declare global{
    interface String {
        double():string;
    }
    interface Window{
        myname:string
    }
}

export  {}
```

## 合并声明

- 同一名称的两个独立声明会被合并成一个单一声明
- 合并后的声明拥有原先两个声明的特性  

关键字|作为类型使用|作为值使用
class|yes|yes
enum|yes|yes
interface|yes|no
type|yes|no
function|no|yes
var,let,const|no|yes

- 类既可以作为类型使用，也可以作为值使用，接口只能作为类型使用

```typescript

class Person{
    name:string=''
}
let p1:Person;//作为类型使用
let p2 = new Person();//作为值使用

interface Animal{
    name:string
}
let a1:Animal;
let a2 = Animal;//接口类型不能用作值
```

### 合并类型声明

- 可以通过接口合并的特性给一个第三方为扩展类型声明
use.js

```typescript
interface Animal{
    name:string
}
let a1:Animal={name:'zf',age:10};
console.log(a1.name);
console.log(a1.age);
//注意不要加export {} ,这是全局的
types\animal\index.d.ts

interface Animal{
    age:number
}
```

###  使用命名空间扩展类

- 我们可以使用 namespace 来扩展类，用于表示内部类

```typescript
class Form {
  username: Form.Item='';
  password: Form.Item='';
}
//Item为Form的内部类
namespace Form {
  export class Item {}
}
let item:Form.Item = new Form.Item();
console.log(item);
```

### 使用命名空间扩展函数

- 我们也可以使用 namespace 来扩展函数

```typescript
function greeting(name: string): string {
    return greeting.words+name;
}

namespace greeting {
    export let words = "Hello,";
}
console.log(greeting('zf'))
```

### 使用命名空间扩展枚举类型

```typescript
enum Color {
    red = 1,
    yellow = 2,
    blue = 3
}

namespace Color {
    export const green=4;
    export const purple=5;
}
console.log(Color.green)
```

### 扩展Store

```typescript
import { createStore, Store } from 'redux';
type StoreExt = Store & {
    ext: string
}
let store: StoreExt = createStore(state => state);
store.ext = 'hello';
```

### 生成声明文件

- 把TS编译成JS后丢失类型声明，我们可以在编译的时候自动生成一份JS文件

```typescript
{
  "compilerOptions": {
     "declaration": true, /* Generates corresponding '.d.ts' file.*/
  }
}
```
