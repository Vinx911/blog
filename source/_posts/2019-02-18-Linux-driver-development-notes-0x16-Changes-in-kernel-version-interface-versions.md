## 2.6.13 - 2.6.14
添加
```c
#define DEFINE_TIMER(_name, _function, _expires, _data)
```
## 2.6.14 - 2.6.15
添加
```c
static inline void setup_timer(struct timer_list * timer,
                              void (*function)(unsigned long),
                               unsigned long data)

```
## 3.6 - 3.7
```c
void init_timer_key(struct timer_list *timer,
                   const char *name,
                   struct lock_class_key *key);
// 改为
void init_timer_key(struct timer_list *timer, unsigned int flags,
                   const char *name, struct lock_class_key *key);

```
删除setup_timer_key

## 4.1 - 4.2
修改
```c
struct timer_list {
	......
	struct tvec_base *base;

	void (*function)(unsigned long);
	unsigned long data;

	int slack;
	......
};
//改为
struct timer_list {
	......
	void			(*function)(unsigned long);
	unsigned long		data;
	u32			flags;
	int			slack;
	......
};
```
## 4.7 - 4.8
1. 删除struct timer_list {}中的
```c
	int			slack;
```
2. 添加`TIMER_PINNED_INITIALIZER`
3. 添加`init_timer_pinned`
## 4.13 - 4.14
添加 
```c
static inline void timer_setup(struct timer_list *timer,
			       void (*callback)(struct timer_list *),
			       unsigned int flags)
```
```c
from_timer(var, callback_timer, timer_fieldname)
```
## 4.14 - 4.15
修改
```c
struct timer_list {
	......
	void			(*function)(unsigned long);
	unsigned long		data;
	u32			flags;
	......
};
//改为
struct timer_list {
	......
	void			(*function)(struct timer_list *);
	u32			flags;
	......
};
```
```c
#define DEFINE_TIMER(_name, _function, _expires, _data)
//改为
#define DEFINE_TIMER(_name, _function) 
```
1. 去除setup_timer函数，改为timer_setup(timer, callback, flags) 
2. 去除init_timer函数
```c
void init_timer_key(struct timer_list *timer, unsigned int flags,
		    const char *name, struct lock_class_key *key);
// 改为
void init_timer_key(struct timer_list *timer,
	  void (*func)(struct timer_list *), unsigned int flags,
	  const char *name, struct lock_class_key *key);
```
