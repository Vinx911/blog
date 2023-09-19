---
title: Linux驱动开发杂记(0x17) - 内核tty接口各版本的变化(一)之tty_flip.h
date: 2019-02-19 09:10:41
id: 23
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---
# Linux驱动开发杂记(0x17) - 内核tty接口各版本的变化(一)之tty_flip.h
## 2.6.15 - 2.6.16
添加
```c
extern int tty_buffer_request_room(struct tty_struct *tty, size_t size);
extern int tty_insert_flip_string(struct tty_struct *tty, unsigned char *chars, size_t size);
extern int tty_insert_flip_string_flags(struct tty_struct *tty, unsigned char *chars, char *flags, size_t size);
extern int tty_prepare_flip_string(struct tty_struct *tty, unsigned char **chars, size_t size);
extern int tty_prepare_flip_string_flags(struct tty_struct *tty, unsigned char **chars, char **flags, size_t size);
```
修改
```c
_INLINE_ void tty_insert_flip_char(struct tty_struct *tty, unsigned char ch, char flag)
//改为
_INLINE_ int tty_insert_flip_char(struct tty_struct *tty, unsigned char ch, char flag)
```

## 2.6.33 - 2.6.34
添加
```c
extern int tty_insert_flip_string_fixed_flag(struct tty_struct *tty, const unsigned char *chars, char flag, size_t size);
```

## 3.8 - 3.9

```c
extern int tty_buffer_request_room(struct tty_struct *tty, size_t size);
extern int tty_insert_flip_string_flags(struct tty_struct *tty, const unsigned char *chars, const char *flags, size_t size);
extern int tty_insert_flip_string_fixed_flag(struct tty_struct *tty, const unsigned char *chars, char flag, size_t size);
extern int tty_prepare_flip_string(struct tty_struct *tty, unsigned char **chars, size_t size);
extern int tty_prepare_flip_string_flags(struct tty_struct *tty, unsigned char **chars, char **flags, size_t size);
void tty_schedule_flip(struct tty_struct *tty);
//改为
extern int tty_buffer_request_room(struct tty_port *port, size_t size);
extern int tty_insert_flip_string_flags(struct tty_port *port,
               const unsigned char *chars, const char *flags, size_t size);
extern int tty_insert_flip_string_fixed_flag(struct tty_port *port,
               const unsigned char *chars, char flag, size_t size);
extern int tty_prepare_flip_string(struct tty_port *port,
               unsigned char **chars, size_t size);
extern int tty_prepare_flip_string_flags(struct tty_port *port,
               unsigned char **chars, char **flags, size_t size);
extern void tty_flip_buffer_push(struct tty_port *port);
void tty_schedule_flip(struct tty_port *port);
```

```c
static inline int tty_insert_flip_char(struct tty_struct *tty, unsigned char ch, char flag)
//改为
static inline int tty_insert_flip_char(struct tty_port *port, unsigned char ch, char flag)
```

```c
static inline int tty_insert_flip_string(struct tty_struct *tty, const unsigned char *chars, size_t size)
//改为
static inline int tty_insert_flip_string(struct tty_port *port,
               const unsigned char *chars, size_t size)
```

## 3.11 - 3.12
添加
```c
extern int tty_buffer_space_avail(struct tty_port *port);
```

```c
extern void tty_buffer_lock_exclusive(struct tty_port *port);
extern void tty_buffer_unlock_exclusive(struct tty_port *port);
```

## 3.13 - 3.14
添加
```c
extern int tty_buffer_set_limit(struct tty_port *port, int limit);
```
删除

```c
extern int tty_prepare_flip_string_flags(struct tty_port *port,
               unsigned char **chars, char **flags, size_t size);
```

