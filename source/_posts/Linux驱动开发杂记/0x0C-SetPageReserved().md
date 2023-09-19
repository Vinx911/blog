---
title: Linux驱动开发杂记(0x0C) - SetPageReserved()
date: 2018-11-15 15:31:01
id: 12
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

﻿SetPageReserved()
　　随着linux的长时间运行，空闲页面会越来越少，为了防止linux内核进入请求页面的僵局中，Linux内核采用页面回收算法(PFRA)从用户进程和内核高速缓存中回收内存页框，并根据需要把要回收页框的内容交换到磁盘上的交换区。调用该函数可以使页面不被交换。
```c
#define SetPageReserved(page) set_bit(PG_reserved,&(page)->flags)
```
PG_reserved 的标志说明如下。
 + PG_reserved is set for special pages, which can nEVEr beswapped out. Some  of them might not EVEn exist (eg empty_bad_page)…
