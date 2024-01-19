---
title: ESP_IDF 二进制文件各个段说明
date: 2023-08-31 08:44:50
id: 100
tags: ESP-IDF
categories: 嵌入式
copyright: true
---

# ESP_IDF 二进制文件各个段说明

## DRAM .data

所有初始化值为非零值的静态变量

分配 D/IRAM 空间

还在二进制映像中占用空间来存储非零初始化值。



## DRAM .bss

所有初始化值为零的静态变量

分配 D/IRAM 空间

不会在 flash 中占用额外空间。



## IRAM0 .text

所有从内部 RAM 执行的代码

 在二进制映像中也会占用空间。



## Flash .text

通过 flash 缓存执行的所有代码



## Flash .rodata

通过 flash 缓存加载的只读数据



