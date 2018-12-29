---
title: Linux驱动开发杂记(0x13) - 串口UART驱动API
date: 2018-11-22 15:13:17
tags: Linux驱动
categories: Linux驱动开发杂记
---

﻿串口驱动API
## 1、uart_register_driver
```c
/* 功能：    uart_register_driver用于将串口驱动uart_driver注册到内核(串口核心层)中，通常在模块初始化函数调用该函数。
 * 参数 drv：要注册的uart_driver

 * 返回值：  成功，返回0；否则返回错误码
 */
int uart_register_driver(struct uart_driver *drv)
```
## 2、uart_unregister_driver
```c
/* 功能：    uart_unregister_driver用于注销我们已注册的uart_driver，通常在模块卸载函数调用该函数
 * 参数 drv：要注销的uart_driver

 * 返回值：  成功，返回0；否则返回错误码
 */
void uart_unregister_driver(struct uart_driver *drv)
```

## 3、uart_add_one_port
```c
/* 功能：    uart_add_one_port用于为串口驱动添加一个串口端口，通常在探测到设备后(驱动的设备probe方法)调用该函数
 * 参数 drv：串口驱动
 *      port:要添加的串口端口

 * 返回值：  成功，返回0；否则返回错误码
 */
int uart_add_one_port(struct uart_driver *drv, struct uart_port *port)
```

## 4、uart_remove_one_port
```c
/* 功能：     uart_remove_one_port用于删除一个已添加到串口驱动中的串口端口，通常在驱动卸载时调用该函数
 * 参数 drv： 串口驱动
 *      port: 要删除的串口端口
 * 返回值：   成功，返回0；否则返回错误码
 */
int uart_remove_one_port(struct uart_driver *drv, struct uart_port *port)
```

## 5、uart_write_wakeup
```c
/* 功能：     uart_write_wakeup唤醒上层因向串口端口写数据而阻塞的进程，通常在串口发送中断处理函数中调用该函数
 * 参数 port：需要唤醒写阻塞进程的串口端口
 */
void uart_write_wakeup(struct uart_port *port)
```

## 6、uart_suspend_port
```c
/* 功能：     uart_suspend_port用于挂起特定的串口端口
 * 参数 drv： 要挂起的串口端口所属的串口驱动
 *      port：要挂起的串口端口
 * 返回值：   成功返回0；否则返回错误码
 */
int uart_suspend_port(struct uart_driver *drv, struct uart_port *port)
```

## 7、uart_resume_port
```c
/* 功能：     uart_resume_port用于恢复某一已挂起的串口
 * 参数 drv： 要恢复的串口端口所属的串口驱动
 *      port：要恢复的串口端口
 * 返回值：   成功返回0；否则返回错误码
 */
int uart_resume_port(struct uart_driver *drv, struct uart_port *port)
```

## 8、uart_get_baud_rate
```c
/* 功能：        uart_get_baud_rate通过解码termios结构体来获取指定串口的波特率
 * 参数 port：  要获取波特率的串口端口
 *     termios：当前期望的termios配置(包含串口波特率)
 *     old：    以前的termios配置，可以为NULL
 *     min：    可接受的最小波特率
 *     max：    可接受的最大波特率
 * 返回值：     串口的波特率
 */
unsigned int
uart_get_baud_rate(struct uart_port *port, struct ktermios *termios,
     struct ktermios *old, unsigned int min, unsigned int max)
```

## 9、uart_get_divisor
```c
/* 功能：     uart_get_divisor用于计算某一波特率的串口时钟分频数（串口波特率除数）
 * 参数 port：要计算时钟分频数的串口端口
 *      baud：期望的波特率
 *返回值：    串口时钟分频数
 */
unsigned int uart_get_divisor(struct uart_port *port, unsigned int baud)
```

## 10、uart_update_timeout
```c
/* 功能：      uart_update_timeout用于更新（设置）串口FIFO超时时间
 * 参数 port： 要更新超时时间的串口端口
 *     cflag：termios结构体的cflag值
 *     baud： 串口的波特率
 */
void uart_update_timeout(struct uart_port *port, unsigned int cflag, unsigned int baud)
```

## 11、uart_match_port
```c
/* 功能：uart_match_port用于判断两串口端口是否为同一端口
 * 参数 port1、port2：要判断的串口端口
 * 返回值：不同返回0；否则返回非0
 */
int uart_match_port(struct uart_port *port1, struct uart_port *port2)
```

## 12、uart_console_write
```c
/* 功能：        uart_console_write用于向串口端口写一控制台信息

 * 参数 port:    要写信息的串口端口
 *     s:       要写的信息
 *     count:   信息的大小
 *     putchar: 用于向串口端口写字符的函数，该函数函数有两个参数：串口端口和要写的字符
 */
void uart_console_write(struct uart_port *port, const char *s,
            unsigned int count,
            void (*putchar)(struct uart_port *, int))
```

## 13、uart_insert_char
```c
/* 功能：        uart_insert_char用于把接收到的字符送进串口驱动的buf

 * 参数 port:    要写信息的串口端口
 *     status： 串口RX缓冲区的状态
 *     overrun：@status中溢出位的掩码
 *     ch：     推送的字符
 * 	flag：   该字符的标志
 */
void uart_insert_char(struct uart_port *port, unsigned int status,
		 unsigned int overrun, unsigned int ch, unsigned int flag)
```

## 14、uart_parse_earlycon
```c
int uart_parse_earlycon(char *p, unsigned char *iotype, resource_size_t *addr,
			char **options)
```

## 15、uart_parse_options
```c
void uart_parse_options(const char *options, int *baud, int *parity,
		   int *bits, int *flow)
```

## 16、uart_set_options
```c
int uart_set_options(struct uart_port *port, struct console *co,
		 int baud, int parity, int bits, int flow)
```

## 17、uart_handle_dcd_change
```c
void uart_handle_dcd_change(struct uart_port *uport, unsigned int status)
```

## 18、uart_handle_cts_change
```c
void uart_handle_cts_change(struct uart_port *uport, unsigned int status)
```

## 19、uart_get_rs485_mode
```c
void uart_get_rs485_mode(struct device *dev, struct serial_rs485 *rs485conf)
```

## 20、circ的函数
```c
/* 缓冲区是否为空 */
#define uart_circ_empty(circ)           ((circ)->head == (circ)->tail) 
/* 清除缓冲区 */
#define uart_circ_clear(circ)           ((circ)->head = (circ)->tail = 0)
/* 缓冲区中有效数据的个数 */
#define uart_circ_chars_pending(circ)   \ 
        (CIRC_CNT((circ)->head, (circ)->tail, UART_XMIT_SIZE))
/* 缓冲区中空闲空间的个数 */
#define uart_circ_chars_free(circ)      \ 
        (CIRC_SPACE((circ)->head, (circ)->tail, UART_XMIT_SIZE))
```
