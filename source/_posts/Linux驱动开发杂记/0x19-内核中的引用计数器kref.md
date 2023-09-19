---
title: Linux驱动开发杂记(0x19) - 内核中的引用计数器kref
date: 2019-06-27 12:00:32
id: 25
tags: Linux驱动
categories: Linux驱动开发杂记
copyright: true
---

# Linux驱动开发杂记(0x19) - 内核中的引用计数器kref

kref是一个引用计数器，它被嵌套进其它的结构中，记录所嵌套结构的引用计数，并在计数清零时调用相应的清理函数。kref的头文件在include/linux/kref.h。

以下代码摘自Linux 4.15.0

## 1. 定义

下面是kref的结构定义

```c
struct kref {
	refcount_t refcount;
};

typedef struct refcount_struct {
	atomic_t refs;
} refcount_t;
```

在Linux 4.11以前的版本定义为

```c
struct kref {
    atomic_t refcount;
};
```

修改定义的原因如下：

> ```
> Use the refcount_t 'atomic' type to implement kref, this makes kref more robust by bringing saturation semantics.
> ```

可以看到kref包含了一个refcount结构体，refcount则包含了一个atomic_t类型的计数值。atomic_t是原子类型，对其操作都要求是原子执行的，有专门的原子操作API执行，即使在多处理器间也保持原子性。使用atomic_t类型充当计数值，就省去了加锁去锁的过程。

默认情况下，kref调用refcount函数的行为与Linux 4.11以前版本kref直接调用原子操作API是完全一样的。通过宏定义**CONFIG_REFCOUNT_FULL**切换到完全状态检查的实现，该实现可以（略微）较慢，但可以提供针对可用于安全漏洞利用的各种免费使用条件的保护。通过宏定义**CONFIG_ARCH_HAS_REFCOUNT**切换到芯片支持的计数器实现。

## 2. 初始化

```c
/**
 * kref_init - initialize object.
 * @kref: object in question.
 */
static inline void kref_init(struct kref *kref)
{
	refcount_set(&kref->refcount, 1);
}

/**
 * refcount_set - set a refcount's value
 * @r: the refcount
 * @n: value to which the refcount will be set
 */
static inline void refcount_set(refcount_t *r, unsigned int n)
{
	atomic_set(&r->refs, n);
}
```

kref初始化时通过调用refcount_set设置可refs的初始计数值。具体计数值设置由原子操作atomic_set完成。在代码中将初始计数值设置为1。

## 3. 读取计数值

```c
static inline unsigned int kref_read(const struct kref *kref)
{
	return refcount_read(&kref->refcount);
}

/**
 * refcount_read - get a refcount's value
 * @r: the refcount
 *
 * Return: the refcount's value
 */
static inline unsigned int refcount_read(const refcount_t *r)
{
	return atomic_read(&r->refs);
}
```

## 4. 增加计数值

```c
/**
 * kref_get - increment refcount for object.
 * @kref: object.
 */
static inline void kref_get(struct kref *kref)
{
	refcount_inc(&kref->refcount);
}

static inline void refcount_inc(refcount_t *r)
{
	atomic_inc(&r->refs);
}
```

kref_get递增kref的计数值。

```c
/**
 * kref_get_unless_zero - Increment refcount for object unless it is zero.
 * @kref: object.
 *
 * Return non-zero if the increment succeeded. Otherwise return 0.
 *
 * This function is intended to simplify locking around refcounting for
 * objects that can be looked up from a lookup structure, and which are
 * removed from that lookup structure in the object destructor.
 * Operations on such objects require at least a read lock around
 * lookup + kref_get, and a write lock around kref_put + remove from lookup
 * structure. Furthermore, RCU implementations become extremely tricky.
 * With a lookup followed by a kref_get_unless_zero *with return value check*
 * locking in the kref_put path can be deferred to the actual removal from
 * the lookup structure and RCU lookups become trivial.
 */
static inline int __must_check kref_get_unless_zero(struct kref *kref)
{
	return refcount_inc_not_zero(&kref->refcount);
}
```

kref_get_unless_zero执行引用计数值加1操作，除非它已经为0。如果计数值加1成功，则返回非0，否则返回0。

## 5. 减少计数值

```c
kref_put/**
 * kref_put - decrement refcount for object.
 * @kref: object.
 * @release: pointer to the function that will clean up the object when the
 *	     last reference to the object is released.
 *	     This pointer is required, and it is not acceptable to pass kfree
 *	     in as this function.  If the caller does pass kfree to this
 *	     function, you will be publicly mocked mercilessly by the kref
 *	     maintainer, and anyone else who happens to notice it.  You have
 *	     been warned.
 *
 * Decrement the refcount, and if 0, call release().
 * Return 1 if the object was removed, otherwise return 0.  Beware, if this
 * function returns 0, you still can not count on the kref from remaining in
 * memory.  Only use the return value if you want to see if the kref is now
 * gone, not present.
 */
static inline int kref_put(struct kref *kref, void (*release)(struct kref *kref))
{
	if (refcount_dec_and_test(&kref->refcount)) {
		release(kref);
		return 1;
	}
	return 0;
}

static inline __must_check bool refcount_dec_and_test(refcount_t *r)
{
	return atomic_dec_and_test(&r->refs);
}
```

kref_get递增kref的计数值。如果计数值减少到0，说明kref指向的结构没有被任何代码引用，生命周期结束，执行release函数释放资源。

```c
static inline int kref_put_mutex(struct kref *kref,
				 void (*release)(struct kref *kref),
				 struct mutex *lock)
{
	if (refcount_dec_and_mutex_lock(&kref->refcount, lock)) {
		release(kref);
		return 1;
	}
	return 0;
}

static inline int kref_put_lock(struct kref *kref,
				void (*release)(struct kref *kref),
				spinlock_t *lock)
{
	if (refcount_dec_and_lock(&kref->refcount, lock)) {
		release(kref);
		return 1;
	}
	return 0;
}
```

kref_put_mutex为在mutex的保护下执行`kref_put`操作。

kref_put_lock为在spinlock的保护下执行kref_put操作。



## 6. kref使用

摘自[kref](https://blog.csdn.net/weixin_44935940/article/details/90733302)

为了方便用户的使用，在内核自带的文档（`Documentation/kref.txt`）中总结了一些 kref 的使用规则和注意事项。以下是文档的翻译：

### 6.1 kref 介绍

对于那些用在各种场合并且被随处传递的对象，你如果没有使用引用计数，那么你的代码很可能存在问题。当然，如果你想要使用引用计数，那么 kref 会是一个不错的选择。

你可以使用下述的方式将 kref 添加到你的数据结构中：

```c
struct my_data {
		struct kref refcount;
		...
};
```

kref 可以出现在数据结构的任意位置。

### 6.2 kref 初始化

```c
struct my_data *data;
data = kmalloc(sizeof(*data), GFP_KERNEL);
if (!data) 
		return -ENOMEM;
kref_init(&data->refcount);
```

此时会将 kref 的引用计数值设置为1。

### 6.3 kref 使用规则
初始化 kref 后，你必须遵循以下规则：

1. 如果你创建了一个非临时的指针，特别是它可能会被传递给另一个线程执行，你必须在传递该指针前执行`kref_get()`
```c
kref_get(&data->refcount);
```

如果你已经有一个指向 kref 的合法指针，也就是说引用计数值不会变成0。那么你在执行上述操作的时候可以不用加锁。

2. 当你使用完这个指针时，你必须调用`kref_put()`
```c
kref_put(&data->refcount, data_release);
```

如果这个指针是某个对象最后的引用，那么相应的 release 函数就会被调用。 如果代码不会在没有拥有合法指针的情况下去获得一个合法的指针，那么在 `kref_put()` 时就不需要加锁。

3. 如果代码试图在没有拥有引用计数的情况下就调用 `kref_get()` ，那么必须串行化 `kref_get()` 和 `kref_put()` 的执行。因为很可能在 `kref_get()` 执行之前或者执行过程中， `kref_put()` 就被调用并把整个数据结构释放掉了。

例如，你分配了一些数据并把它传递到其它的线程去处理：

```c
void data_release(struct kref *ref)
{
		struct my_data *data = container_of(ref, struct my_data, refcount);
		kfree(data);
}

void more_data_handling(void *cb_data)
{
		struct my_data *data = cb_data;

		/* do stuff with data here */
		kref_put(&data->refcount, data_release);
}

int my_data_handler(void)
{
		int rv = 0;
		struct my_data *data;
		struct task_struct *task;
		data = kmalloc(sizeof(*data), GFP_KERNEL);
		if (!data)
				return -ENOMEM;
		kref_init(&data->refcount);
		kref_get(&data->refcount);
		task = kthread_run(more_data_handling, data, "more_data_handling");
		if (task == ERR_PTR(-ENOMEM)) { 
				rv = -ENOMEM;
				kref_put(&data->refcount, data_release);
				goto out;
		}
		/* do stuff with data here */
out:
		kref_put(&data->refcount, data_release);
		return rv;
}
```


这样一来，无论两个线程的执行顺序如何都没有关系，`kref_put()` 知道数据何时不再有引用计数，可以被销毁。

此处需要注意规则1中的要求，必须在传递指针之前调用 `kref_get()` ，而不能像下面一样操作：

```c
	task = kthread_run(more_data_handling, data, "more_data_handling");
	if (task == ERR_PTR(-ENOMEM)) { 
			rv = -ENOMEM;
			goto out;
	} else /* BAD BAD BAD - get is after the handoff */
			kref_get(&data->refcount);
```

不要认为自己在使用上面代码时知道自己正在做什么。首先，你可能并不清楚自己正在做什么。其次，虽然你可能知道自己在做什么，但一些修改或复制你代码的人可能并不知道他们正在做什么。这是一种糟糕的使用方式。千万不要这样做。

当然在某些情况下也可以对 `kref_put` 和 `kref_get` 做一些优化。例如，你已经完成了对这个数据的处理，并准备把它传递给其它地方，这里就不需要再做多余的 `kref_put` 和 `kref_get` 了。

```c
/* Silly extra get and put */
kref_get(&obj->ref);
enqueue(obj);
kref_put(&obj->ref, obj_cleanup);
```

直接做 enqueue 操作即可。当然，在这个地方添加一条注释也是非常受欢迎的。

```c
enqueue(obj);
/* We are done with obj, so we pass our refcount off to the queue. DON'T TOUCH obj AFTER HERE! */
```


最后一条规则（规则3）处理起来较麻烦。例如，你有一列数据，每个数据都有 kref 计数，同时你想获取第一条数据。但你不能简单地把第一条数据从链表中取出并调用 `kref_get()`。因为这违背了第三条规则：你并没有拥有一个合法的指针（引用计数）。因此，你必须添加一个锁。如下所示：

```c
static DEFINE_MUTEX(mutex);
static LIST_HEAD(q);
struct my_data {
		struct kref      refcount;
		struct list_head link;
};

static struct my_data *get_entry()
{
		struct my_data *entry = NULL;
		mutex_lock(&mutex);
		if (!list_empty(&q)) { 
				entry = container_of(q.next, struct my_data, link);
				kref_get(&entry->refcount);
		}
		mutex_unlock(&mutex);
		return entry;
}

static void release_entry(struct kref *ref)
{
		struct my_data *entry = container_of(ref, struct my_data, refcount);
		list_del(&entry->link);
		kfree(entry); ///< 执行过程中处于加锁状态
}

static void put_entry(struct my_data *entry)
{
		mutex_lock(&mutex);
		kref_put(&entry->refcount, release_entry);
		mutex_unlock(&mutex);
}
```

如果你不想在整个过程中都加锁，那么 `kref_put()` 的返回值就非常有用。你不想在加锁的情况下调用kfree。那么你可以使用下述的方式：

```c
static void release_entry(struct kref *ref)
{
		/* All work is done after the return from kref_put(). */
}

static void put_entry(struct my_data *entry)
{
		mutex_lock(&mutex);
		if (kref_put(&entry->refcount, release_entry)) { 
				list_del(&entry->link);
				mutex_unlock(&mutex);
				kfree(entry);
		} else 
				mutex_unlock(&mutex);
}
```

如果在 release() 的时候需要花费较长的时间，或者又需要得到这把相同的锁，那么上述的操作就非常有用。但需要注意的是，在 release() 中完成释放工作会使代码看起来更整洁。

另外，上述的例子还可以通过使用 `kref_get_unless_zero()` 函数来进一步优化：

```c
static struct my_data *get_entry()
{
		struct my_data *entry = NULL;
		mutex_lock(&mutex);
		if (!list_empty(&q)) { 
				entry = container_of(q.next, struct my_data, link);
				/* 如果此时已调用kref_put()，引用计数为0，则entry重新被置为NULL */
				if (!kref_get_unless_zero(&entry->refcount)) 
						entry = NULL; 
		}
		mutex_unlock(&mutex);
		return entry;
}

static void release_entry(struct kref *ref)
{
		struct my_data *entry = container_of(ref, struct my_data, refcount);
		mutex_lock(&mutex);
		list_del(&entry->link);
		mutex_unlock(&mutex);
		kfree(entry);
}

static void put_entry(struct my_data *entry)
{
		kref_put(&entry->refcount, release_entry);
}
```

上述操作可以有效的去除 put_entry() 中 kref_put()周围的锁。

### 6.4 kref 和 RCU
  在使用函数 `kref_get_unless_zero()` 的情况下，上术示例中也可以通过 RCU 来查找：

```c
struct my_data {
		struct rcu_head rhead;
		struct kref refcount;
		...
};

static struct my_data *get_entry_rcu()
{
		struct my_data *entry = NULL;
		rcu_read_lock();
		if (!list_empty(&q)) { 
				entry = container_of(q.next, struct my_data, link);
				if (!kref_get_unless_zero(&entry->refcount))
						entry = NULL;
		}
		rcu_read_unlock();
		return entry;
}

static void release_entry_rcu(struct kref *ref)
{
		struct my_data *entry = container_of(ref, struct my_data, refcount);
		mutex_lock(&mutex);
		list_del_rcu(&entry->link);
		mutex_unlock(&mutex);
		kfree_rcu(entry, rhead);
}

static void put_entry(struct my_data *entry)
{
		kref_put(&entry->refcount, release_entry_rcu);
}
```

