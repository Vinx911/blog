---
title: TypeScript学习笔记 - 基础类型
date: 2022-11-04 18:58:06
tags: TypeScript
categories: TypeScript学习笔记
copyright: true
---

## 布尔值(boolean)

```Typescript
let isDone: boolean = false;
```

## 数字(number)

‌‌‌　　所有数字都是浮点数。 这些浮点数的类型是 `number`。

```Typescript
let decLiteral: number = 6; 
let hexLiteral: number = 0xf00d; 
let binaryLiteral: number = 0b1010; 
let octalLiteral: number = 0o744;
```

## 字符串(string)

‌‌‌　　可以使用双引号（ `"`）或单引号（`'`）表示字符串。

```Typescript
‌‌‌　　let name: string = "bob"; name = "smith";
```
‌‌‌　　你还可以使用 _模版字符串_ ，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围(\`)，并且以`${ expr }`这种形式嵌入表达式

```Typescript
‌‌‌　　let name: string = `Gene`; 
‌‌‌　　let age: number = 37; 
‌‌‌　　let sentence: string = `Hello, my name is ${ name }.

‌‌‌　　I'll be ${ age + 1 } years old next month.`;
```

## 数组

‌‌‌　　第一种，可以在元素类型后面接上 `[]`，表示由此类型元素组成的一个数组：

```Typescript
‌‌‌　　let list: number[] = [1, 2, 3];
```

‌‌‌　　第二种方式是使用数组泛型，`Array<元素类型>`：

```Typescript
‌‌‌　　let list: Array<number> = [1, 2, 3];
```

## 元组 Tuple

‌‌‌　　元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```Typescript
‌‌‌　　// Declare a tuple type 
‌‌‌　　let x: [string, number]; 
‌‌‌　　// Initialize it 
‌‌‌　　x = ['hello', 10]; // OK 
‌‌‌　　// Initialize it incorrectly 
‌‌‌　　x = [10, 'hello']; // Error
```

‌‌‌　　当访问一个已知索引的元素，会得到正确的类型：

```Typescript
‌‌‌　　console.log(x[0].substr(1)); // OK 
‌‌‌　　console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```

‌‌‌　　当访问一个越界的元素，会使用联合类型替代：

```Typescript
‌‌‌　　x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
‌‌‌　　console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString  
‌‌‌　　x[6] = true; // Error, 布尔不是(string | number)类型
```

## 枚举

‌‌‌　　`enum`类型是对JavaScript标准数据类型的一个补充。 像C#等其它语言一样，使用枚举类型可以为一组数值赋予友好的名字。

```Typescript
‌‌‌　　enum Color {Red, Green, Blue}
‌‌‌　　enum Color {Red = 1, Green, Blue} 
‌‌‌　　enum Color {Red = 1, Green = 2, Blue = 4} 
‌‌‌　　let c: Color = Color.Green;
```

‌‌‌　　默认情况下，从`0`开始为元素编号。 你也可以手动的指定成员的数值或者全部都采用手动赋值

## Any

‌‌‌　　不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 `any`类型来标记这些变量

```Typescript
‌‌‌　　let notSure: any = 4; 
‌‌‌　　notSure = "maybe a string instead";
‌‌‌　　notSure = false; // okay, definitely a boolean

‌‌‌　　let list: any[] = [1, true, "free"];
```

## Void

‌‌‌　　某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 `void`：

```Typescript
‌‌‌　　function warnUser(): void {
	console.log("This is my warning message"); 
‌‌‌　　}
```
‌‌‌
　　声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

```Typescript
‌‌‌　　let unusable: void = undefined;
```

## Null 和 Undefined

‌‌‌　　TypeScript里，`undefined`和`null`两者各自有自己的类型分别叫做`undefined`和`null`。 和 `void`相似，它们的本身的类型用处不是很大：

```Typescript
‌‌‌　　// Not much else we can assign to these variables! 
‌‌‌　　let u: undefined = undefined;
‌‌‌　　let n: null = null;
```

‌‌‌　　默认情况下`null`和`undefined`是所有类型的子类型。

## Never

‌‌‌　　`never`类型表示的是那些永不存在的值的类型。
‌‌‌　　`never`类型是任何类型的子类型，也可以赋值给任何类型；

```Typescript
‌‌‌　　// 返回never的函数必须存在无法达到的终点 
‌‌‌　　function error(message: string): never {
	throw new Error(message);
‌‌‌　　}  // 推断的返回值类型为never

‌‌‌　　function fail() {
	return error("Something failed");
‌‌‌　　}  // 返回never的函数必须存在无法达到的终点

‌‌‌　　function infiniteLoop(): never {
	while (true) {     } 
‌‌‌　　}
```

## Object

‌‌‌　　`object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。

‌‌‌　　使用`object`类型，就可以更好的表示像`Object.create`这样的API。例如：

```Typescript
‌‌‌　　declare function create(o: object | null): void;
‌‌‌　　create({ prop: 0 }); // OK 
‌‌‌　　create(null); // OK  
‌‌‌　　create(42); // Error 
‌‌‌　　create("string"); // Error 
‌‌‌　　create(false); // Error 
‌‌‌　　create(undefined); // Error
```

# 类型断言(强制类型转换)

‌‌‌　　类型断言有两种形式。 其一是“尖括号”语法：

```Typescript
‌‌‌　　let someValue: any = "this is a string";  
‌‌‌　　let strLength: number = (<string>someValue).length;
```

‌‌‌　　另一个为`as`语法：

```Typescript
‌‌‌　　let someValue: any = "this is a string";  
‌‌‌　　let strLength: number = (someValue as string).length;
```

‌‌‌　　当你在TypeScript里使用JSX时，只有 `as`语法断言是被允许的。
