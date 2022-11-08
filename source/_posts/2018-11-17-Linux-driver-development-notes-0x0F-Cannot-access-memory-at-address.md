---
title: Linux驱动开发杂记(0x0F) - Cannot access memory at address
date: 2018-11-17 00:54:15
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

﻿按照教程[Linux内核调试环境搭建(基于ubuntu12.04)](http://www.netfairy.net/?post=242) 配置kgdb双机调试时，出现一下错误：
```
(gdb) set serial baud 115200
(gdb) target remote /dev/ttyS0
Remote debugging using /dev/ttyS0
0xffffffffb753b940 in ?? ()
(gdb) b start_kernel
Breakpoint 1 at 0xffffffff82819ad0: file init/main.c, line 515.
(gdb) c
Continuing.
Warning:
Cannot insert breakpoint 1.
Cannot access memory at address 0xffffffff82819ad0
```

解决方法是：在grub.cfg中添加`rodata=off nokaslr`

也就是在
```
 linux /boot/vmlinuz-2.6.32.61 root=UUID=f8c18f80-7723-470b-98a852566006 ro
```
后面 和 
```
quiet
```
前面加以下内容
```
kgdb=ttyS0,115200 kgdboc=ttyS0,115200  kgdbwait rodata=off nokaslr
```
