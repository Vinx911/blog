---
title: Linux驱动开发杂记(0x11) - uart_port结构体
date: 2018-11-22 15:07:57
id: 17
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

﻿```c
struct uart_port {
    spinlock_t             lock;           /* 串口端口锁 */
    unsigned int           iobase;         /* IO端口基地址 */
    unsigned char __iomem *membase;        /* IO内存基地址,经映射(如ioremap)后的IO内存虚拟基地址 */
    unsigned int           irq;            /* 中断号 */
    unsigned int           uartclk;        /* 串口时钟 */
    unsigned int           fifosize;       /* 串口FIFO缓冲大小 */
    unsigned char          x_char;         /* xon/xoff字符 */
    unsigned char          regshift;       /* 寄存器位移 */
    unsigned char          iotype;         /* IO访问方式 */
    unsigned char          unused1;

#define UPIO_PORT        (0)               /* IO端口 */
#define UPIO_HUB6        (1)
#define UPIO_MEM         (2)               /* IO内存 */
#define UPIO_MEM32       (3)
#define UPIO_AU          (4)               /* Au1x00 type IO */
#define UPIO_TSI         (5)               /* Tsi108/109 type IO */
#define UPIO_DWAPB       (6)               /* DesignWare APB UART */
#define UPIO_RM9000      (7)               /* RM9000 type IO */

    unsigned int        read_status_mask;  /* 关心的Rx error status */
    unsigned int        ignore_status_mask;/* 忽略的Rx error status */
    struct uart_info      *info;           /* pointer to parent info */
    struct uart_icount     icount;         /* 计数器 */
    
    struct console        *cons;           /* console结构体 */
#ifdef CONFIG_SERIAL_CORE_CONSOLE
    unsigned long         sysrq;           /* sysrq timeout */
#endif

    upf_t                 flags;

#define UPF_FOURPORT         ((__force upf_t) (1 << 1))
#define UPF_SAK              ((__force upf_t) (1 << 2))
#define UPF_SPD_MASK         ((__force upf_t) (0x1030))
#define UPF_SPD_HI           ((__force upf_t) (0x0010))
#define UPF_SPD_VHI          ((__force upf_t) (0x0020))
#define UPF_SPD_CUST         ((__force upf_t) (0x0030))
#define UPF_SPD_SHI          ((__force upf_t) (0x1000))
#define UPF_SPD_WARP         ((__force upf_t) (0x1010))
#define UPF_SKIP_TEST        ((__force upf_t) (1 << 6))
#define UPF_AUTO_IRQ         ((__force upf_t) (1 << 7))
#define UPF_HARDPPS_CD       ((__force upf_t) (1 << 11))
#define UPF_LOW_LATENCY      ((__force upf_t) (1 << 13))
#define UPF_BUGGY_UART       ((__force upf_t) (1 << 14))
#define UPF_MAGIC_MULTIPLIER ((__force upf_t) (1 << 16))
#define UPF_CONS_FLOW        ((__force upf_t) (1 << 23))
#define UPF_SHARE_IRQ        ((__force upf_t) (1 << 24))
#define UPF_BOOT_AUTOCONF    ((__force upf_t) (1 << 28))
#define UPF_FIXED_PORT       ((__force upf_t) (1 << 29))
#define UPF_DEAD             ((__force upf_t) (1 << 30))
#define UPF_IOREMAP          ((__force upf_t) (1 << 31))

#define UPF_CHANGE_MASK      ((__force upf_t) (0x17fff))
#define UPF_USR_MASK         ((__force upf_t) (UPF_SPD_MASK|UPF_LOW_LATENCY))

    unsigned int             mctrl;        /* 当前的moden设置 */
    unsigned int             timeout;      /* character-based timeout */        
    unsigned int             type;         /* 端口类型 */
    const struct uart_ops   *ops;          /* 串口端口操作函数集 */
    unsigned int             custom_divisor;
    unsigned int             line;         /* 端口索引 uart_driver.dev_name加上line组成串口的设备节点的名字 */
    resource_size_t          mapbase;      /* IO内存物理基地址，可用于ioremap */
    struct device           *dev;          /* 父设备 */
    unsigned char            hub6;         /* this should be in the 8250 driver */
    unsigned char            suspended;
    unsigned char            unused[2];
    void                    *private_data; /* 端口私有数据,一般为platform数据指针 */
};
```
