---
title: Linux驱动开发杂记(0x14) - 环形缓冲区circ_buf
date: 2018-12-07 09:37:30
tags: Linux驱动
categories: Linux驱动开发杂记
---

```c
/*
 * 在内核<linux/circ_buf.h>中定义了关于环形缓冲区的相关变量。
 * 其中缓冲区的大小size必须定义成2的n次方，这样可以将取余运算转换成位与运算，
 * 提高计算机的处理速度。例如：x % size = x & (size - 1)。实际可用的空间为size-1，
 * 这样可以避免缓冲区为满时和为空时都满足head = tail。
*/
struct circ_buf {
	char *buf;	// 指向分配缓冲区起始地址的指针
	int head;   // 指向生产者向缓冲区放入数据的位置
	int tail;  	// 指向消费者从缓冲区取走数据的位置
};

/* Return count in buffer.  */
/* 计算缓冲区中有效数据的大小； */
#define CIRC_CNT(head,tail,size) (((head) - (tail)) & ((size)-1))

/* Return space available, 0..size-1.  We always leave one free char
   as a completely full buffer has head == tail, which is the same as
   empty.  */
/* 计算缓冲区剩余空闲空间的大小； */
#define CIRC_SPACE(head,tail,size) CIRC_CNT((tail),((head)+1),(size))

/* Return count up to the end of the buffer.  Carefully avoid
   accessing head and tail more than once, so they can change
   underneath us without returning inconsistent results.  */
/* 计算tail到缓冲区尾部的数据大小，即一次性可以读取的数据大小；*/
#define CIRC_CNT_TO_END(head,tail,size) \
	({int end = (size) - (tail); \
	  int n = ((head) + end) & ((size)-1); \
	  n < end ? n : end;})

/* Return space available up to the end of the buffer.  */
/* 计算head到缓冲区尾部的剩余空间，即一次性可以写入的数据大小； */
#define CIRC_SPACE_TO_END(head,tail,size) \
	({int end = (size) - 1 - (head); \
	  int n = (end + (tail)) & ((size)-1); \
	  n <= end ? n : end+1;})
```
