---
title: Linux驱动开发杂记(0x1A) - 内核中的原子操作
date: 2019-06-27 13:40:18
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

# Linux驱动开发杂记(0x1A) - 内核中的原子操作

具体的接口API函数整理如下：

| 接口函数            | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| atomic_inc          | 原子变量的值加一                                             |
| atomic_inc_return   | 同上，只不过将变量v的最新值返回                              |
| atomic_inc_and_test | 递增v指向的原子变量，并测试是否为0。若为0，返回真，否则返回假 |
| atomic_dec          | 原子变量的值减去一                                           |
| atomic_dec_return   | 同上，只不过将变量v的最新值返回                              |
| atomic_dec_and_test | 递减v指向的原子变量，并测试是否为0。若为0，返回真，否则返回假 |
| atomic_add          | 给一个原子变量v增加i                                         |
| atomic_sub          | 给一个原子变量v减去i                                         |
| atomic_sub_and_test | 给一个原子变量v减去i，并判断变量v的最新值是否等于0           |
| atomic_add_negative | 将v指向的原子变量加上i，并测试结果是否为负。若为负，返回真，否则返回假 |
| atomic_read         | 读取v指向的原子变量的值                                      |
| atomic_set          | 设置v指向的原子变量的值为i                                   |

 

```c

// 原子变量的值加一
static inline void atomic_inc(atomic_t *v)
{
	atomic_add_return(1, v);
}
// 同上，只不过将变量v的最新值返回
#define atomic_inc_return(v)		atomic_add_return(1, (v))
// 递增v指向的原子变量，并测试是否为0。若为0，返回真，否则返回假
#define atomic_inc_and_test(v)		(atomic_inc_return(v) == 0)

// 原子变量的值减去一
static inline void atomic_dec(atomic_t *v)
{
	atomic_sub_return(1, v);
}
// 同上，只不过将变量v的最新值返回
#define atomic_dec_return(v)		atomic_sub_return(1, (v))
// 递减v指向的原子变量，并测试是否为0。若为0，返回真，否则返回假
#define atomic_dec_and_test(v)		(atomic_dec_return(v) == 0)


// 给一个原子变量v增加i
static inline void atomic_add(int i, atomic_t *v)
{
	atomic_add_return(i, v);
}

// 给一个原子变量v减去i
static inline void atomic_sub(int i, atomic_t *v)
{
	atomic_sub_return(i, v);
}
// 给一个原子变量v减去i，并判断变量v的最新值是否等于0
#define atomic_sub_and_test(i, v)	(atomic_sub_return((i), (v)) == 0)


// 将v指向的原子变量加上i，并测试结果是否为负。若为负，返回真，否则返回假
static inline int atomic_add_negative(int i, atomic_t *v)
{
	return atomic_add_return(i, v) < 0;
}

// 读取v指向的原子变量的值
#define atomic_read(v)	READ_ONCE((v)->counter)
// 设置v指向的原子变量的值为i
#define atomic_set(v, i) WRITE_ONCE(((v)->counter), (i))

```

