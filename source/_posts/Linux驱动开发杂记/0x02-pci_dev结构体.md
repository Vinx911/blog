---
title: Linux驱动开发杂记(0x02) - pci_dev结构体
date: 2018-11-08 16:32:43
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

﻿每种类的PCI设备都可以用结构类型pci_dev来描述。更为准确地说，应该是每一个PCI功能，即PCI逻辑设备都唯一地对应有一个pci_dev设备描述符。该数据结构的部分定义如下（include/linux/pci.h）：

```c
struct pci_dev {
/* 总线设备链表元素bus_list：每一个pci_dev结构除了链接到全局设备链表中外，还会通过这个成员连接到
其所属PCI总线的设备链表中。每一条PCI总线都维护一条它自己的设备链表视图，以便描述所有连接在该
PCI总线上的设备，其表头由PCI总线的pci_bus结构中的 devices成员所描述t*/
struct list_head bus_list;
/* 总线指针bus：指向这个PCI设备所在的PCI总线的pci_bus结构。因此，对于桥设备而言，bus指针将指向
桥设备的主总线（primary bus），也即指向桥设备所在的PCI总线*/
struct pci_bus *bus;
/* 指针subordinate：指向这个PCI设备所桥接的下级总线。这个指针成员仅对桥设备才有意义，而对于一般
的非桥PCI设备而言，该指针成员总是为NULL*/
struct pci_bus *subordinate;
/* 无类型指针sysdata：指向一片特定于系统的扩展数据*/
void *sysdata;
/* 指针procent：指向该PCI设备在／proc文件系统中对应的目录项*/
struct proc_dir_entry *procent;
/* devfn：这个PCI设备的设备功能号，也成为PCI逻辑设备号（0－255）。其中bit[7:3]是物理设备号（取值
范围0－31），bit[2:0]是功能号（取值范围0－7）。 */
unsigned int devfn;
/* vendor：这是一个16无符号整数，表示PCI设备的厂商ID*/
unsigned short vendor;
/*device：这是一个16无符号整数，表示PCI设备的设备ID */
unsigned short device;
/* subsystem_vendor：这是一个16无符号整数，表示PCI设备的子系统厂商ID*/
unsigned short subsystem_vendor;
/* subsystem_device：这是一个16无符号整数，表示PCI设备的子系统设备ID。*/
unsigned short subsystem_device;
/* class：32位的无符号整数，表示该PCI设备的类别，其中，bit［7：0］为编程接口，bit［15：8］为子类
别代码，bit ［23：16］为基类别代码，bit［31：24］无意义。显然，class成员的低3字节刚好对应与PCI配
置空间中的类代码*/
unsigned int class;
/* hdr_type：8位符号整数，表示PCI配置空间头部的类型。其中，bit［7］＝1表示这是一个多功能设备，
bit［7］＝0表示这是一个单功能设备。Bit［6：0］则表示PCI配置空间头部的布局类型，值00h表示这是一
个一般PCI设备的配置空间头部，值01h表示这是一个PCI-to-PCI桥的配置空间头部，值02h表示CardBus桥
的配置空间头部*/
u8 hdr_type;
/* rom_base_reg：8位无符号整数，表示PCI配置空间中的ROM基地址寄存器在PCI配置空间中的位置。
ROM基地址寄存器在不同类型的PCI配置空间头部的位置是不一样的，对于type 0的配置空间布局，ROM基
地址寄存器的起始位置是30h，而对于PCI-to-PCI桥所用的type 1配置空间布局，ROM基地址寄存器的起始
位置是38h*/
u8 rom_base_reg;
/* 指针driver：指向这个PCI设备所对应的驱动程序定义的pci_driver结构。每一个pci设备驱动程序都必须定
义它自己的pci_driver结构来描述它自己。*/
struct pci_driver *driver;
/*dma_mask：用于DMA的总线地址掩码，一般来说，这个成员的值是0xffffffff。数据类型dma_addr_t定义在
include/asm/types.h中，在x86平台上，dma_addr_t类型就是u32类型*/
u64 dma_mask;
/* 当前操作状态 */
pci_power_t  current_state;
/* 通用的设备接口*/
 struct device dev;
/* 无符号的整数irq：表示这个PCI设备通过哪根IRQ输入线产生中断，一般为0－15之间的某个值 */
unsigned int irq;
/*表示该设备可能用到的资源，包括：I/O断口区域、设备内存地址区域以及扩展ROM地址区域。*/
struct resource resource[DEVICE_COUNT_RESOURCE];
/* 配置空间的大小 */
int cfg_size;
/* 透明 PCI 桥 */
unsigned int transparent:1;
/* 多功能设备*/
unsigned int multifunction:1;
/* 设备是主设备*/
unsigned int is_busmaster:1;
/* 设备不使用msi*/
unsigned int no_msi:1;
/* 配置空间访问形式用块的形式 */
unsigned int block_ucfg_access:1;
/* 在挂起时保存配置空间*/
u32 saved_config_space[16];
/* sysfs ROM入口的属性描述*/
struct bin_attribute *rom_attr;
/* 能显示rom 属性*/
int rom_attr_enabled;
/* 资源的sysfs文件*/
struct bin_attribute *res_attr[DEVICE_COUNT_RESOURCE];

};
```

