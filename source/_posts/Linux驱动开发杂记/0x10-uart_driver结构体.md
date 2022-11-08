---
title: Linux驱动开发杂记(0x10) - uart_driver结构体
date: 2018-11-22 15:06:46
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
permalink: Linux-driver-development-notes/0x10-uart_driver-structure.html
---

﻿```c
struct uart_driver {
    struct module     *owner;           /* 拥有该uart_driver的模块,一般为THIS_MODULE */
    const char        *driver_name;     /* 串口驱动名，串口设备文件名以驱动名为基础 */
    const char        *dev_name;        /* 串口设备名 */
    int                major;           /* 主设备号 */
    int                minor;           /* 次设备号 */
    int                nr;              /* 该uart_driver支持的串口个数(最大) */
    struct console    *cons;            /* 其对应的console.若该uart_driver支持serial console,否则为NULL */

    /*
     * these are private; the low level driver should not
     * touch these; they should be initialised to NULL
     */
    struct uart_state *state;
    struct tty_driver *tty_driver;
};
```
