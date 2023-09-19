---
title: Linux驱动开发杂记(0x04) - pci_device_id结构体
date: 2018-11-08 17:05:18
id: 4
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

```c
struct pci_device_id {
	__u32 vendor, device;		/* 厂商和设备ID，Vendor and device ID or PCI_ANY_ID*/
	__u32 subvendor, subdevice;	/* 子系统和设备ID，Subsystem ID's or PCI_ANY_ID */
	__u32 class, class_mask;	/* 类、子类、prog-if三元组，(class,subclass,prog-if) triplet */
	kernel_ulong_t driver_data;	/* 驱动私有数据，Data private to the driver */
};
```
pci_device_id 用MODULE_DEVICE_TABLE宏导出到用户空间。
