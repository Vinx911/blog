---
title: Linux驱动开发杂记(0x0D) - remap_pfn_range()
date: 2018-11-15 16:23:43
id: 13
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: false
---

﻿驱动实现mmap主要是调用：
```c
int remap_pfn_range(struct vm_area_struct *, unsigned long addr,
            unsigned long pfn, unsigned long size, pgprot_t);
```

函数来映射,声明在include/linux/mm.h。 
第一个参数：虚拟地址描述结构体(声明在include/linux/mm_types.h,起始mm.h中已经包含了它)，一般是系统传递下来。 
第二个参数：虚拟起始地址 
第三个参数：物理地址 
第四个参数：映射空间大小，单位字节 
第五个参数：给新 VMA 要求的”protection”. 驱动直接使用 vma->vm_page_prot

返回值，成功返回0，否则返回-1；

用法例子：
```c
#include <linux/slab.h>
#include <linux/mm.h>

static int simple_remap_mmap(struct file *filp, struct vm_area_struct *vma);

struct file_operations fo = {
    ...
    .mmap = simple_remap_mmap,
    ...
};
static int simple_remap_mmap(struct file *filp, struct vm_area_struct *vma)
{
    void *p;
    p = kmalloc(200,GFP_KERNEL)
    if(!p)
        return -1;  
    if (remap_pfn_range(vma, vma->vm_start, virt_to_phys(p),
    vma->vm_end - vma->vm_start,
    vma->vm_page_prot))
    return -EAGAIN; 
    return 0;
}
```
