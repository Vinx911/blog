---
title: TypeScript学习笔记 - 额外的属性检测
date: 2022-11-04 18:58:06
id: 34
tags: TypeScript
categories: TypeScript学习笔记
copyright: true
---

先来看个例子：

```typescript
interface Config {
    width?: number;
}

function  CalculateAreas （config: Config）: { area: number} {
    let square = 100;
    if (config.width) {
        square = config.width * config.width;
    }
    return square;
}

let mySquare = CalculateAreas({ widdth: 5 });
```

我们传入的参数是`widdth`，并不是`width`。

此时TypeScript会认为这段代码可能存在问题。对象字面量当被赋值给变量或作为参数传递的时候，会被特殊对待而且经过“额外属性检查”。

如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。

```typescript
// error: 'widdth' not expected in type 'Config'
let mySquare = CalculateAreas({ widdth: 5 });
```

解决方法，官网上给了三种方式：

第一种使用类型断言：

```typescript
let mySquare = CalculateAreas({ widdth: 5 } as Config);
```

第二种添加字符串索引签名：

```typescript
interface Config {
   width?: number;
   [propName: string]: any;
}
```

这样Config可以有任意数量的属性，并且只要不是width，那么就无所谓他们的类型是什么了。

第三种将字面量赋值给另外一个变量：

```typescript
let options = { widdth: 5 };
let mySquare = CalculateAreas(options);
```
