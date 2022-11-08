---
title: Linux驱动开发杂记(0x07) - I/O端口
date: 2018-11-12 10:06:31
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

﻿## 1 申请I/O 端⼝：
在驱动还没独占设备之前，不应对端⼝进⾏操作。内核提供了⼀个注册接⼝，以允许驱动声明其需要的端⼝：
```c
/* request_region告诉内核：要使⽤first开始的n个端⼝。参数name为设备名。
如果分配成功返回值是⾮NULL；否则无法使用需要的端口(/proc/ioports包含了系统当前所有端口的分配信息，
若request_region分配失败时，可以查看该文件，看谁先用了你要的端口) */
struct resource *request_region(unsigned long first, unsigned long n, const char *name);
```
## 2 访问IO端⼝：
在驱动成功请求到I/O 端⼝后，就可以读写这些端⼝了。⼤部分硬件会将8位、16位和32位端⼝区分开，⽆法像访问内存那样混淆使⽤。驱动程序必须调⽤不同的函数来访问不同⼤⼩的端⼝。
Linux 内核头⽂件(体系依赖的头⽂件<asm/io.h>) 定义了下列内联函数来存取I/O端⼝:
```c
/* inb/outb:读/写字节端⼝(8位宽)。有些体系将port参数定义为unsigned long；
⽽有些平台则将它定义为unsigned short。inb的返回类型也是依赖体系的 */
unsigned inb(unsigned port);
void outb(unsigned char byte, unsigned port);
/* inw/outw:读/写字端⼝(16位宽） */
unsigned inw(unsigned port);
void outw(unsigned short word, unsigned port);
/* inl/outl:读/写32位端⼝。longword也是依赖体系的，有的体系为unsigned long；⽽有的为unsigned int */
unsigned inl(unsigned port);
void outl(unsigned longword, unsigned port);
```
## 3 释放IO端⼝：
```c
/* ⽤完I/O端⼝后(可能在模块卸载时)，应当调⽤release_region将I/O端⼝返还给系统。
参数start和n应与之前传递给request_region⼀致 */
void release_region(unsigned long start, unsigned long n);
```


