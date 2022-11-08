---
title: Linux驱动开发杂记(0x12) - uart_ops结构体
date: 2018-11-22 15:09:30
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

﻿```c
/*
 * This structure describes all the operations that can be
 * done on the physical hardware.
 */
struct uart_ops {
    unsigned int (*tx_empty)(struct uart_port *); /* 串口的Tx FIFO缓存是否为空。如果为空，函数应返回TIOCSER_TEMT，否则返回0。如果端口不支持此操作，返回TIOCSER_TEMT。*/
    void         (*set_mctrl)(struct uart_port *, unsigned int mctrl); /* 设置串口modem控制 */
    unsigned int (*get_mctrl)(struct uart_port *); /* 获取串口modem控制 */
    void         (*stop_tx)(struct uart_port *); /* 禁止串口发送数据 */
    void         (*start_tx)(struct uart_port *); /* 使能串口发送数据 */
    void         (*send_xchar)(struct uart_port *, char ch);/* 发送xChar */
    void         (*stop_rx)(struct uart_port *); /* 禁止串口接收数据 */
    void         (*enable_ms)(struct uart_port *); /* 使能modem的状态信号 */
    void         (*break_ctl)(struct uart_port *, int ctl); /* 设置break信号 */
    int          (*startup)(struct uart_port *); /* 启动串口,应用程序打开串口设备文件时,该函数会被调用 */
    void         (*shutdown)(struct uart_port *); /* 关闭串口,应用程序关闭串口设备文件时,该函数会被调用 */
    void         (*set_termios)(struct uart_port *, struct ktermios *new, struct ktermios *old); /* 设置串口参数 */
    void         (*pm)(struct uart_port *, unsigned int state,
             unsigned int oldstate); /* 串口电源管理 */
    int          (*set_wake)(struct uart_port *, unsigned int state); /*  */
    const char  *(*type)(struct uart_port *); /* 返回一描述串口类型的字符串 */
    void         (*release_port)(struct uart_port *); /* 释放串口已申请的IO端口/IO内存资源,必要时还需iounmap */
    int          (*request_port)(struct uart_port *); /* 申请必要的IO端口/IO内存资源,必要时还可以重新映射串口端口 */
    void         (*config_port)(struct uart_port *, int); /* 执行串口所需的自动配置 */
    int          (*verify_port)(struct uart_port *, struct serial_struct *); /* 核实新串口的信息 */
    int          (*ioctl)(struct uart_port *, unsigned int, unsigned long); /* IO控制 */
};
```
## tx_empty(port)
此函数检查发送fifo和移位通过“端口”中描述的端口是否为空。如果为空，函数应返回TIOCSER_TEMT，否则返回0。如果端口不支持此操作，返回TIOCSER_TEMT。
+ 锁：none
+ 中断：取决于调用者；
+ 此调用不休眠。

## set_mctrl(port, mctrl)

此函数设置串口modem控制模式。mctrl相关的位是：
 - TIOCM_RTS       RTS signal.
 - TIOCM_DTR      DTR signal.
 - TIOCM_OUT1    OUT1 signal.
 - TIOCM_OUT2    OUT2 signal.
 - TIOCM_LOOP    设置端口为回环模式

如果相应的位被置位，该信号应被驱动为有效；如果该位清空，该信号应被驱动为无效。

+ 锁定：采用port->lock

+ 中断：本地禁用

+ 此调用不休眠。

 

## get_mctrl(port)

返回modem控制输入的当前状态。不应返回输出状态，因为核心驱动追踪它们的状态。状态信息应包含：
 - TIOCM_DCD      state of DCD signal
 - TIOCM_CTS       state of CTS signal
 - TIOCM_DSR      state of DSR signal
 - TIOCM_RI          state of RI signal

如果信号当前是有效地，位被设置。如果端口不支持CTS、DCD 或DSR，驱动程序应指明该信号是持续有效的。如果RI不可用，该信号不应表示为有效。
+ 锁定：采用port->lock
+ 中断：本地禁用
+ 此调用必须不休眠。 

## stop_tx(port)

停止发送字符。这可能由于CTS线路变为非活动状态，或tty层表明由于XOFF字符我们要停止传输。
驱动程序应尽快停止传输字符。
+ 锁定：采用port->lock
+ 中断：本地禁用
+ 此调用不休眠。 

## start_tx(port)
开始传输字符。
+ 锁定：采用port->lock
+ 中断：本地禁用
+ 此调用不休眠。 

## stop_rx(port)
停止接收字符。端口处于即将关闭进程中。
+ 锁定：采用port->lock
+ 中断：本地禁用
+ 此调用不休眠。 

## enable_ms(port)
使能modem状态中断。
此方法可被多次调用，当调用shutdown方法时，modem状态中断禁用。
+ 锁定：采用port->lock
+ 中断：本地禁用
+ 此调用不休眠。 

## break_ctl(port,ctl)
控制中断信号的传输。如果ctl非零，中断信号应被传输。当另一个调用作为零ctl时，此信号应终止。
+ 锁定：none
+ 中断：取决于调用者
+ 此调用不休眠。 

## startup(port)
抓取任意中断资源，并初始化任一底层驱动状态。使能接收端口。不应该启用RTS和DTR；可通过一个单独的调用set_mctrl来完成。
此方法只有在端口最初打开时被调用。
+ 锁定：采用port_sem
+ 中断：全局禁用。 

## shutdown(port)
禁用端口，禁用任何可能有效地中断条件，以及释放任何中断资源。不应该禁用RTS和DTR；可通过一个单独的调用set_mctrl来完成。
一旦调用完成，驱动程序不能访问port->info。
此方法只有当没有更多的用户端口时才被调用。
+ 锁定：采用port_sem
+ 中断：取决于调用者 

## flush_buffer(port)
刷新任何写缓冲区，重置所有的DMA状态，并停止正在进行的DMA传输。
每当port->info->xmit循环缓冲区清零时都被调用。
+ 锁定：采用port->lock
+ 中断：本地禁用
+ 此调用不能休眠。 

## set_termios(port,termios,oldtermios)

改变端口参数，包括：字长、奇偶校验、停止位。更新read_status_mask而忽略状态掩码，表示我们有兴趣接收的事件类型。相应的termios->c_cflag位是：
+ CSIZE    - word size
+ CSTOPB - 2 stop bits
+ PARENB - parity enable
+ PARODD- oddparity (when PARENB is in force)
+ CREAD  - enable reception of characters (if not set,still receive characters from the port, but throwthem away.
+ CRTSCTS       - if set, enable CTS status changereporting

+ CLOCAL - if not set, enable modem status change  reporting.

相应的termios->c_iflag位是：
+ INPCK    - enable frame and parity error events to be passed to the TTY layer.
+ BRKINT
+ PARMRK       - both of these enable break events to be passedto the TTY layer.
+ IGNPAR - ignore parity and framing errors
+ IGNBRK - ignore break errors,  If IGNPAR is also set,ignore overrun errors as well.

iflag位的相互作用如下（奇偶校验错误为例）：

|Parity| error |    INPCK   | IGNPAR|
|----|----|----|----|
|n/a|0|n/a|character received, marked as TTY_NORMAL|
|None|1|n/a|character received, marked as TTY_NORMAL|
|Yes|1|0|character received,marked as TTY_PARITY|
|Yes|1|1|character discarded|

如果你的硬件支持硬件“软”流量控制，其他标志可以使用（例如，XON / XOFF字符）。
+ 锁定：none
+ 中断：取决于调用者
+ 此调用不能休眠。 

## pm(port,state,oldstate)
在指定的端口上执行任何电源管理有关的活动。状态表示新状态（定义为ACPI D0-D3），旧状态表示以前状态。从本质上讲，D0表示完全供电，D3指断电。

此函数不应用于抓取任何资源。

当端口最初打开，最终关闭，除非该端口也是系统控制台时，这才被调用。即使CONFIG_PM没被设置，也会发生。
+ 锁定：none
+ 中断：取决于调用者。

## type(port)
返回一个指向字符串常量、描述指定端口的指针，或返回NULL，在这种情况下，字符串“unknow”被替换。
+ 锁定：none
+ 中断：取决于调用者。 

## release_port(port)
释放目前在该端口使用的任何内存和IO区域资源。
+ 锁定：none
+ 中断：取决于调用者。 

## request_port(port)
请求端口所需任意内存和IO区域资源。如果有任何失败，当该函数返回时，没有注册资源，它应该返回-EBUSY失败。
+ 锁定：none
+ 中断：取决于调用者。 

## config_port(port,type)
执行所需端口的自动配置步骤。`type`包括所需配置的位掩码。UART_CONFIG_TYPE表示端口需要探测和识别。如果未检测到端口的类型，port->type应设置为已建立的类型或PORT_UNKNOWN。

UART_CONFIG_IRQ表示应采用标准内核自动探测技术探测的中断信号的自动配置。端口有中断的内部硬连接平台是内有必要的（例如，片上系统的实现）。

+ 锁定：none
+ 中断：取决于调用者。 

## verify_port(port,serinfo)
验证包含在serinfo新的串口信息适于此端口类型。
+ 锁定：none
+ 中断：取决于调用者。 

## ioctl(port,cmd,arg)
执行任何特定端口的IOCTLs。Ioctl命令使用标准编号系统定义，参见<asm/ioctl.h>。
+ 锁定：none
+ 中断：取决于调用者。

