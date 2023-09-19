---
title: Linux驱动开发杂记(0x03) - pci_bus结构体
date: 2018-11-08 16:56:20
id: 3
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

```c
struct pci_bus {
	/* 链表元素node：对于PCI根总线而言，其pci_bus结构通过node成员链接到本节一开始所述的根总线链表中，根总线链表
	的表头由一个list_head类型的全局变量pci_root_buses所描述。而对于非根pci总线，其pci_bus结构通过node成员链接
	到其父总线的子总线链表children中*/
	struct list_head node;
	/*parent指针：指向该pci总线的父总线，即pci桥所在的那条总线*/
	struct pci_bus *parent;
	/*children指针：描述了这条PCI总线的子总线链表的表头。这条PCI总线的所有子总线都通过上述的node链表元素链接成一
	条子总线链表，而该链表的表头就由父总线的children指针所描述*/
	struct list_head children;
	/* list of devices on this bus */
	struct list_head devices;
	/* devices链表头：描述了这条PCI总线的逻辑设备链表的表头。除了链接在全局PCI设备链表中之外，每一个PCI逻辑设备也
	通过其 pci_dev结构中的bus_list成员链入其所在PCI总线的局部设备链表中，而这个局部的总线设备链表的表头就由
	pci_bus结构中的 devices成员所描述*/
	struct pci_dev *self;
	/* 资源指针数组：指向应路由到这条pci总线的地址空间资源，通常是指向对应桥设备的pci_dev结构中的资源数组resource［10：7］*/
	struct resource *resource[PCI_BUS_NUM_RESOURCES];
	/* 指针ops：指向一个pci_ops结构，表示这条pci总线所使用的配置空间访问函数*/
	struct pci_ops *ops;
	/* 无类型指针sysdata：指向系统特定的扩展数据*/
	void *sysdata;
	/* 指针procdir：指向该PCI总线在／proc文件系统中对应的目录项*/
	struct proc_dir_entry *procdir;
	/* number：这条PCI总线的总线编号（bus number），取值范围0－255 */
	 unsigned char number;
	/* primary：表示引出这条PCI总线的“桥设备的主总线”（也即桥设备所在的PCI总线）编号，取值范围0－255*/
	unsigned char primary;/* secondary：表示引出这条PCI总线的桥设备的次总线号，因此secondary成员总是等于number成员的值。取值范围0－255*/
	unsigned char secondary;
	/* subordinate：这条PCI总线的下属PCI总线（Subordinate pci bus）的总线编号最大值，它应该等于引出这条PCI总线的桥设备的subordinate值*/
	unsigned char subordinate;
	/* name［48］：这条PCI总线的名字字符串*/
	char name[48];
	unsigned short  bridge_ctl;
	unsigned short  pad2;
	struct device *bridge;
	struct device class_dev;
	struct bin_attribute *legacy_io;
	struct bin_attribute *legacy_mem;
};
```
