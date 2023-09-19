---
title: TypeScript学习笔记 - 接口
date: 2022-11-04 18:58:06
id: 35
tags: TypeScript
categories: TypeScript学习笔记
copyright: true
---

---
number headings: auto, first-level 1, max 6, 1.1 contents ^top
---

```typescript
interface LabelledValue { 
	label: string; 
}
```

# 1 可选属性

接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。
带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个`?`符号。
```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}
```

# 2 只读属性

一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用 `readonly`来指定只读属性:
```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}
```

TypeScript具有`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改
```typescript
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
```

可以用类型断言重写：
```typescript
a = ro as number[];
```

## 2.1 `readonly` vs `const`

最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 `const`，若做为属性则使用`readonly`。

# 3 函数类型

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。
```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```
这样定义后，我们可以像使用其它接口一样使用这个函数类型的接口。 
```typescript
let mySearch: SearchFunc = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```
函数的参数会逐个进行检查，要求对应位置上的参数类型是兼容的。

# 4 可索引的类型
可索引类型具有一个  _索引签名_ ，它描述了对象索引的类型，还有相应的索引返回值类型。
```typescript
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。

可以将索引签名设置为只读，这样就防止了给索引赋值：
```typescript
interface ReadonlyStringArray {
	readonly [index: number]: string; 
} 
let myArray: ReadonlyStringArray = ["Alice", "Bob"]; 
myArray[2] = "Mallory"; // error!
```
不能设置`myArray[2]`，因为索引签名是只读的。

# 5 类类型

## 5.1 实现接口
```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```
接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员。

## 5.2 类静态部分与实例部分的区别
```typescript
interface ClockConstructor {
  new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
/*报错：Class 'Clock' incorrectly implements interface 'ClockConstructor'.
  Type 'Clock' provides no match for the signature 'new (hour: number, minute: number): any'.*/
  currentTime: Date;
  constructor(h: number, m: number) {}
}
```
类实现接口的时候，只检查实例部分，而类里的constructor属于静态部分，所以不检查。不能检查他，但是你又确确实实在接口里写了，就报错了

那我们一定要用类实现接口，要在接口里用构造器签名怎么办？**官网再次给出了解答**

```typescript
interface ClockConstructor {
  new (hour: number, minute: number);
}

interface ClockInterface {
  tick();
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
};

let c =new Clock(12,4);
```

# 6 继承接口

和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。
```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

```

# 7 混合类型
一个对象可以同时做为函数和对象使用，并带有额外的属性。

```typescript
interface Counter {
    (start: number): string; // 函数
    interval: number;        // 额外的属性
    reset(): void;           // 额外的属性
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

# 8 接口继承类
当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。
```typescript
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {
    select() { }
}

// 错误：“Image”类型缺少“state”属性。
class Image implements SelectableControl {
    select() { }
}

class Location {

}
```
