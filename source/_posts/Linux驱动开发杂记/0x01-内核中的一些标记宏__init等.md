---
title: Linux驱动开发杂记(0x01) - 内核中的一些标记宏__init等
date: 2018-11-08 14:59:12
id: 1
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: false
---

﻿> 以下内容摘自[内核初始化优化宏 ,初始化顺序， __init，__devexit等](https://blog.csdn.net/lanmanck/article/details/4718378), 本文仅作为笔记保存。

内核使用了大量不同的宏来标记具有不同作用的函数和数据结构。如宏__init 、__devinit 等。这些宏在include/linux/init.h 头文件中定义。编译器通过这些宏可以把代码优化放到合适的内存位置，以减少内存占用和提高内核效率。

下面是一些常用的宏：

## __init 
标记内核启动时使用的初始化代码，内核启动完成后不再需要。以此标记的代码位于.init.text 内存区域。

## __exit 
标记退出代码，对于非模块无效。

## __initdata 
标记内核启动时使用的初始化数据结构，内核启动完成后不再需要。以此标记的代码位于.init.data 内存区域。

## __devinit
标记设备初始化使用的代码。

## __devinitdata 
标记初始化设备数据结构的函数。

## __devexit
标记移除设备时使用的代码。

## xxx_initcall
一系列的初始化代码，按降序优先级排列。

初始化代码的特点是：在系统启动运行，且一旦运行后马上退出内存，不再占用内存。

对于驱动程序模块来说，这些优化标记使用的情况如下：

+ 通过module_init() 和module_exit() 函数调用的函数就需要使用__init 和__exit 宏来标记。
+ pci_driver 数据结构不需标记。
+ probe() 和remove() 函数应该使用__devinit 和__devexit 标记，且只能标记probe() 和remove()
+ 如果remove() 使用__devexit 标记，则在pci_driver 结构中要用__devexit_p(remove) 来引用remove() 函数。
+ 如果你不确定需不需要添加优化宏则不要添加。

