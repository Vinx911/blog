---
title: Linux驱动开发杂记(0x09) - likely()与unlikely()
date: 2018-11-12 13:35:12
id: 9
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: false
---

﻿likely()与unlikely()在2.6内核中，随处可见，那为什么要用它们？它们之间有什么区别呢？
首先明确：
if (likely(value))等价于if (value)
if (unlikely(value))等价于if (value)

也就是说likely()和unlikely()从阅读和理解的角度是一样的。
这两个宏在内核中定义如下：
```c
#define likely(x) __builtin_expect(!!(x), 1)
#define unlikely(x) __builtin_expect(!!(x), 0)
```
这里的__built_expect()函数是gcc(version >= 2.96)的内建函数,提供给程序员使用的，目的是将"分支转移"的信息提供给编译器，这样编译器对代码进行优化，以减少指令跳转带来的性能下降。
__buildin_expect((x), 1)表示x的值为真的可能性更大.
__buildin_expect((x), 0)表示x的值为假的可能性更大.
也就是说，使用likely(),执行if后面的语句的机会更大，使用unlikely(),执行else后面的语句机会更大一些。
