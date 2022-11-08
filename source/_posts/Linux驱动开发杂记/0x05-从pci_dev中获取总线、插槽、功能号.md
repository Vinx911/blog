---
title: Linux驱动开发杂记(0x05) - 从pci_dev中获取总线、插槽、功能号
date: 2018-11-08 17:10:12
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
permalink: Linux-driver-development-notes/0x05-Get-bus-slot-function-number-from-pci_dev.html
---

```c
/*
 * The PCI interface treats multi-function devices as independent
 * devices.  The slot/function address of each device is encoded
 * in a single byte as follows:
 *
 *	7:3 = slot
 *	2:0 = function
 */
 #define PCI_DEVFN(slot, func)	((((slot) & 0x1f) << 3) | ((func) & 0x07))
 #define PCI_SLOT(devfn)		(((devfn) >> 3) & 0x1f)
 #define PCI_FUNC(devfn)		((devfn) & 0x07)
```
```c
struct pci_dev *dev

BusNumber    = dev->bus->number;
SlotNumber   = PCI_SLOT(dev->devfn);
FuncNumber = PCI_FUNC(dev->devfn);
```
