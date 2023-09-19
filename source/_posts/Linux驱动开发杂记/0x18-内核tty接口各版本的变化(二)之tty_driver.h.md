---
title: Linux驱动开发杂记(0x18) - 内核tty接口各版本的变化(二)之tty_driver.h
date: 2019-02-19 09:59:02
id: 24
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---
# Linux驱动开发杂记(0x18) - 内核tty接口各版本的变化(二)之tty_driver.h
## 2.6.17 - 2.6.18
删除
```c
struct tty_driver {
	const char      *devfs_name;
}
```
## 2.6.19 - 2.6.20
修改
```c
struct tty_operations {
	void (*set_termios)(struct tty_struct *tty, struct termios * old);
//改为	
	void (*set_termios)(struct tty_struct *tty, struct ktermios * old);
}
```
```c
struct tty_driver {
	struct termios init_termios; /* Initial termios */
	//改为	
	struct ktermios init_termios; /* Initial termios */

	struct termios **termios;
	struct termios **termios_locked;
	//改为	
	struct ktermios **termios;
	struct ktermios **termios_locked;

	void (*set_termios)(struct tty_struct *tty, struct termios * old);
	//改为	
	void (*set_termios)(struct tty_struct *tty, struct ktermios * old);
}
```
## 2.6.21 - 2.6.22
添加
```c
struct tty_operations {
	long (*compat_ioctl)(struct tty_struct *tty, struct file * file,
				unsigned int cmd, unsigned long arg);
}
```
```c
struct tty_driver {
	long (*compat_ioctl)(struct tty_struct *tty, struct file * file,
                           unsigned int cmd, unsigned long arg);
}
```
## 2.6.25 - 2.6.26
添加
```c
extern struct tty_driver *tty_find_polling_driver(char *name, int *line);
```
```c
struct tty_driver {
	const struct tty_operations *ops;
}
```

修改
```c
struct tty_operations {
	void (*put_char)(struct tty_struct *tty, unsigned char ch);
	//改为
	int  (*put_char)(struct tty_struct *tty, unsigned char ch);
}
```
```c
struct tty_driver {
	long (*compat_ioctl)(struct tty_struct *tty, struct file * file,
                           unsigned int cmd, unsigned long arg);
}
```
删除
```c
struct tty_operations {
	int (*write_proc)(struct file *file, const char __user *buffer,
                        unsigned long count, void *data);
}
```
```c
struct tty_driver {
	int  (*open)(struct tty_struct * tty, struct file * filp);
	void (*close)(struct tty_struct * tty, struct file * filp);
	int  (*write)(struct tty_struct * tty,
	              const unsigned char *buf, int count);
	void (*put_char)(struct tty_struct *tty, unsigned char ch);
	void (*flush_chars)(struct tty_struct *tty);
	int  (*write_room)(struct tty_struct *tty);
	int  (*chars_in_buffer)(struct tty_struct *tty);
	int  (*ioctl)(struct tty_struct *tty, struct file * file,
	            unsigned int cmd, unsigned long arg);
	long (*compat_ioctl)(struct tty_struct *tty, struct file * file,
	                     unsigned int cmd, unsigned long arg);
	void (*set_termios)(struct tty_struct *tty, struct ktermios * old);
	void (*throttle)(struct tty_struct * tty);
	void (*unthrottle)(struct tty_struct * tty);
	void (*stop)(struct tty_struct *tty);
	void (*start)(struct tty_struct *tty);
	void (*hangup)(struct tty_struct *tty);
	void (*break_ctl)(struct tty_struct *tty, int state);
	void (*flush_buffer)(struct tty_struct *tty);
	void (*set_ldisc)(struct tty_struct *tty);
	void (*wait_until_sent)(struct tty_struct *tty, int timeout);
	void (*send_xchar)(struct tty_struct *tty, char ch);
	int (*read_proc)(char *page, char **start, off_t off,
	                 int count, int *eof, void *data);
	int (*write_proc)(struct file *file, const char __user *buffer,
	                  unsigned long count, void *data);
	int (*tiocmget)(struct tty_struct *tty, struct file *file);
	int (*tiocmset)(struct tty_struct *tty, struct file *file,
	                unsigned int set, unsigned int clear);
}
```

## 2.6.26 - 2.6.27
添加
```c
struct tty_operations {
	int (*resize)(struct tty_struct *tty, struct tty_struct *real_tty, struct winsize *ws);
}
```
修改
```c
struct tty_operations {	
	void (*break_ctl)(struct tty_struct *tty, int state);
//改为
	int (*break_ctl)(struct tty_struct *tty, int state);
}
```

## 2.6.29 - 2.6.30
添加
```c
struct tty_operations {
	struct tty_struct * (*lookup)(struct tty_driver *driver, struct inode *inode, int idx);
	int  (*install)(struct tty_driver *driver, struct tty_struct *tty);
	void (*remove)(struct tty_driver *driver, struct tty_struct *tty);
	void (*shutdown)(struct tty_struct *tty);
	int (*set_termiox)(struct tty_struct *tty, struct termiox *tnew);
}
```
```c
struct tty_driver {
	struct kref kref;       /* Reference management */
}
```

```c
extern void tty_driver_kref_put(struct tty_driver *driver);
extern inline struct tty_driver *tty_driver_kref_get(struct tty_driver *d);
```

删除
```c
struct tty_driver {
	int     refcount;       /* for loadable tty drivers */
}
```

## 2.6.28 - 2.6.29
修改
```c
struct tty_operations {
	int (*resize)(struct tty_struct *tty, struct tty_struct *real_tty, unsigned int rows, unsigned int cols);
	int (*resize)(struct tty_struct *tty, struct winsize *ws);
}
```

## 2.6.29 - 2.6.30
添加
```c
struct tty_operations {
	const struct file_operations *proc_fops;
}
```
删除
```c
struct tty_operations {
	int (*read_proc)(char *page, char **start, off_t off, int count, int *eof, void *data);

}
```

## 2.6.31 - 2.6.32
添加
```c
struct tty_operations {
	void (*cleanup)(struct tty_struct *tty);
}
```

## 2.6.36 - 2.6.37
添加
```c
struct tty_operations {
	int (*get_icount)(struct tty_struct *tty, struct serial_icounter_struct *icount);
}
```
## 2.6.38 - 2.6.39
//修改
```c
struct tty_operations {
	int  (*ioctl)(struct tty_struct *tty, struct file * file, unsigned int cmd, unsigned long arg);
	// 改为
	int  (*ioctl)(struct tty_struct *tty, unsigned int cmd, unsigned long arg);
	
	long (*compat_ioctl)(struct tty_struct *tty, struct file * file,unsigned int cmd, unsigned long arg);
	// 改为
	long (*compat_ioctl)(struct tty_struct *tty,unsigned int cmd, unsigned long arg);
	
	int (*tiocmget)(struct tty_struct *tty, struct file *file);
	int (*tiocmset)(struct tty_struct *tty, struct file *file,unsigned int set, unsigned int clear);
	// 改为
	int (*tiocmget)(struct tty_struct *tty);
	int (*tiocmset)(struct tty_struct *tty, unsigned int set, unsigned int clear);
}                        
```

## 3.2 - 3.3
删除
```c
struct tty_driver {
	......
	struct ktermios **termios_locked;
	......
}
```
## 3.3 - 3.4
删除
```c
struct tty_driver {
	......
	int     minor_num;      /* number of *possible* devices */
	......
}
```

## 3.6 - 3.7
添加
```c
struct tty_driver {
	......
	struct tty_port **ports;
	......
}
```

```c
#define tty_alloc_driver(lines, flags)
```

修改
```c
struct tty_driver {
	......
	struct cdev cdev;
	// 改为
	struct cdev *cdevs;
	......
}
```

## 4.2 - 4.3
修改
```c
struct tty_driver {
	......
	struct cdev *cdevs;
	// 改为
	struct cdev **cdevs;
	......
}
```
## 4.5 - 4.6
修改
```c
struct tty_operations {
	......
	struct tty_struct * (*lookup)(struct tty_driver *self, int idx);
	// 改为
	struct tty_struct * (*lookup)(struct tty_driver *self, struct file *, int idx);
	......
}
```
```
struct tty_struct * (*lookup)(struct tty_driver *self, int idx)
struct tty_struct * (*lookup)(struct tty_driver *self, struct file *, int idx)
```

## 4.13 - 4.14
添加
```c
struct tty_operations {
	......
	void (*show_fdinfo)(struct tty_struct *tty, struct seq_file *m);
	......
}
```
## 4.17 - 4.18
添加
```c
struct tty_operations {
	......
	int (*proc_show)(struct seq_file *, void *);
	......
}
```
删除
```c
struct tty_operations {
	......
	const struct file_operations *proc_fops;
	......
}
```


## 4.19 - 4.20
添加
```c
struct tty_operations {
	......
	int  (*get_serial)(struct tty_struct *tty, struct serial_struct *p);
	int  (*set_serial)(struct tty_struct *tty, struct serial_struct *p);
	......
}
```

