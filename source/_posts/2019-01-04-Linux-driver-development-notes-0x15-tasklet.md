---
title: Linux驱动开发杂记(0x15) - tasklet
date: 2019-01-04 11:48:10
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---
　　为了提高系统的响应能力和并发能力，Linux将中断处理分了上半部和下半部。当一个中断产生，调用该中断对应的处理程序(上半部)，然后告诉系统，对应的后半部可以执行了，中断处理程序立即返回，下半部会在合适的时机由操作系统调用。这样一来就大大的减少了中断处理所需要的时间。

tasklet是中断处理下半部分最常用的一种方法，驱动程序一般先申请中断，在中断处理函数内完成中断上半部分的工作后调用tasklet。

## 一、tasklet的特点
1. tasklet只可以在一个CPU上同步地执行，不同的tasklet可以在不同地CPU上同步地执行。
2. tasklet的实现是建立在两个软件中断的基础之上的，即HI_SOFTIRQ和TASKLET_SOFTIRQ，本质上没有什么区别，只不过HI_SOFTIRQ的优先级更高一些
3. 由于tasklet是在软中断上实现的，所以像软中断一样不能睡眠、不能阻塞，处理函数内不能含有导致睡眠的动作，如减少信号量、从用户空间拷贝数据或手工分配内存等。
4. 一个 tasklet 能够被禁止并且之后被重新使能; 它不会执行直到它被使能的次数与被禁止的次数相同.
5. tasklet的串行化使tasklet函数不必是可重入的，因此简化了设备驱动程序开发者的工作。
6. 每个cpu拥有一个tasklet_vec链表，具体是哪个cpu的tasklet_vec链表，是根据当前线程是运行在哪个cpu来决定的。

## 二、tasklet结构体
```c
struct tasklet_struct {
	struct tasklet_struct 	*next;		/*指向链表中的下一个结构*/
	unsigned long 			state;		/* 小任务的状态 */
	atomic_t 				count;		/* 引用计数器 */
	void (*func) (unsigned long);		/* 要调用的函数 */
	unsigned long 			data;		/* 传递给函数的参数 */
}；
```
+ State域的取值为TASKLET_STATE_SCHED或TASKLET_STATE_RUN。TASKLET_STATE_SCHED表示小任务已被调度，正准备投入运行，TASKLET_STATE_RUN表示小任务正在运行。

+ Count域是小任务的引用计数器。如果它不为0，则小任务被禁止，不允许执行；只有当它为零，小任务才被激活，并且在被设置为挂起时，小任务才能够执行。

## 三、tasklet的定义
tasklet既可以静态创建也可以动态创建。
### 1、静态创建
定义变量名为name的tasklets_struct变量，并初始化调用函数为func，参数为data，使能tasklet。
```c
DECLARE_TASKLET(name, func, data);
#define DECLARE_TASKLET(name, func, data) \
	struct tasklet_struct name = { NULL, 0, ATOMIC_INIT(0), func, data }
```
定义变量名为name的tasklets_struct变量，并初始化调用函数为func，参数为data，禁止tasklet。
```c
DECLARE_TASKLET_DISABLED(name, func, data)；
#define DECLARE_TASKLET_DISABLED(name, func, data) \
	struct tasklet_struct name = { NULL, 0, ATOMIC_INIT(1), func, data }
```
### 2、动态创建
先定义struct tasklet_struct name;然后初始化 
```c
void tasklet_init(struct tasklet_struct *t,void (*func)(unsigned long), unsigned long data)
{
    t->next = NULL;              //
    t->state = 0;                //设置为未调度 未运行  
    atomic_set(&t->count, 0);    //默认使能
    t->func = func;              //调用函数
    t->data = data;              //调用函数参数
}
```
## 四、调度tasklet
通过调用tasklet_schedule()函数并传递给它相应的tasklt_struct指针，该小任务就会被调度以便适当的时候执行：
```c
static inline void tasklet_schedule(struct tasklet_struct *t)；
tasklet_schedule(&my_tasklet);  /*把 my_tasklet 标记为挂起 */
```
在小任务被调度以后，只要有机会它就会尽可能早的运行。在它还没有得到运行机会之前，如果一个相同的小任务又被调度了，那么它仍然只会运行一次。
       可以调用tasklet_disable()函数来禁止某个指定的小任务。如果该小任务当前正在执行，这个函数会等到它执行完毕再返回。调用tasklet_enable()函数可以激活一个小任务，如果希望把以DECLARE_TASKLET_DISABLED（）创建的小任务激活，也得调用这个函数，如：
```c
tasklet_disable(&my_tasklet);     /* 小任务现在被禁止,这个小任务不能运行 */
tasklet_enable(&my_tasklet);    /*  小任务现在被激活 */
```
也可以调用tasklet_kill()函数从挂起的队列中去掉一个小任务。该函数的参数是一个指向某个小任务的tasklet_struct的长指针。在小任务重新调度它自身的时候，从挂起的队列中移去已调度的小任务会很有用。这个函数首先等待该小任务执行完毕，然后再将它移去。

## 五、tasklet相关函数
```c
void tasklet_disable(struct tasklet_struct *t);
```
这个函数禁止给定的 tasklet. tasklet 可能仍然被 tasklet_schedule 调度, 但是它的执行被延后直到这个 tasklet 被再次使能. 如果这个 tasklet 当前在运行,这个函数忙等待直到这个 tasklet 退出; 因此, 在调用 tasklet_disable 后, 你可以确保这个 tasklet 在系统任何地方都不在运行.
```c
void tasklet_disable_nosync(struct tasklet_struct *t);
```
禁止这个 tasklet, 但是没有等待任何当前运行的函数退出. 当它返回, 这个tasklt 被禁止并且不会在以后被调度直到重新使能, 但是它可能仍然运行在另一个 CPU 当这个函数返回时.
```c
void tasklet_enable(struct tasklet_struct *t);
```
使能一个之前被禁止的 tasklet. 如果这个 tasklet 已经被调度, 它会很快运行.一个对 tasklet_enable 的调用必须匹配每个对 tasklet_disable 的调用, 因为内核跟踪每个 tasklet 的"禁止次数".
```c
void tasklet_schedule(struct tasklet_struct *t);
```
调度 tasklet 执行. 如果一个 tasklet 被再次调度在它有机会运行前, 它只运行一次. 但是, 如果他在运行中被调度, 它在完成后再次运行; 这保证了在其他事件被处理当中发生的事件收到应有的注意. 这个做法也允许一个 tasklet 重新调度它自己.
```c
void tasklet_hi_schedule(struct tasklet_struct *t);
```
调度 tasklet 在更高优先级执行. 当软中断处理运行时, 它处理高优先级tasklet 在其他软中断之前, 包括"正常的" tasklet. 理想地, 只有具有低响应周期要求( 例如填充音频缓冲 )应当使用这个函数, 为避免其他软件中断处理引入的附加周期. 实际上, /proc/jitasklethi 没有显示可见的与 /proc/jitasklet 的区别.
```c
void tasklet_kill(struct tasklet_struct *t);
```
这个函数确保了这个 tasklet 没被再次调度来运行; 它常常被调用当一个设备正被关闭或者模块卸载时. 如果这个 tasklet 被调度来运行, 这个函数等待直到它已执行. 如果这个 tasklet 重新调度它自己, 你必须阻止在调用 tasklet_kill前它重新调度它自己, 如同使用 del_timer_sync.

## 六、tasklet使用模板
```c
/* 定义tasklet和底半部函数并将它们关联 */
void xxx_do_tasklet(unsigned long);
DECLARE_TASKLET(xxx_tasklet, xxx_do_tasklet, 0);

/* 中断处理底半部 */
void xxx_do_tasklet(unsigned long)
{
    //...
}

/* 中断处理顶半部 */
irqreturn_t xxx_interrupt(int irq, void *dev_id)
{
    //...
    tasklet_schedule(&xxx_tasklet);
    //...
}

/* 设备驱动模块加载函数 */
int __init xxx_init(void)
{
    //...
    /* 申请中断 */
    result = request_irq(xxx_irq, xxx_interrupt, 0, "xxx", NULL);
    //...
    return IRQ_HANDLED;
}

/* 设备驱动模块卸载函数 */
void __exit xxx_exit(void)
{
    //...
    /* 释放中断 */
    free_irq(xxx_irq, xxx_interrupt);
    //...
}
```
