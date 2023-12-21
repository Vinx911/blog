---
title: ARM GCC生成固件的压缩、减少和优化大小的几个编译链接参数
date: 2023-11-21 11:02:52
id: 97
tags: ARM GCC
categories: 嵌入式
copyright: true
---

# ARM GCC生成固件的压缩、减少和优化大小的几个编译链接参数

### 1、C++ 编译参数 `-ffunction-sections  -fdata-sections` 和链接参数 `--gc-sections`。

使用`-ffunction-sections`, `-fdata-sections`会使compiler为每个function和data item分配独立的section。

使用 `--gc-sections`会使ld删除没有被使用的section。

就可以压缩固件大小的目的了

### 2、C++ 编译参数 `-fno-rtti -fno-exceptions`.

`-fno-rtti`  Don't generate runtime type idertification (RTTI) 

不使用RTTI

`-fno-exceptions` Don't catch exceptions 

不捕获程序异常

### 3、C++ 编译参数 `-Os`

 Optimize gennerated code(for size) [-Os]

优化代码大小，除了-Os外 还有 -O0 -O1 -O2 -O3 -Ofast.。

### 4、链接参数`---specs=nano.specs`

使用静态库 libc_nano.a  libstdc++_nano.a libsupc++_nano.a替换默认的静态库 libc.a  libstdc++.a libsupc++.a这样就可以大大减小固件了。

### 5、另外还有 -g -g1 -g2 -g3 ,这些都是调试信息，也会影响生成固件的大小。
