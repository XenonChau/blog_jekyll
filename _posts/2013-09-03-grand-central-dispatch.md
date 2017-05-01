---
layout: post
title:  "强大的多线程编程－－GCD"
modified:   2013-09-03 19:22:19 +0800
categories: Objective-C
---

> [Grand Central Dispatch (GCD) Reference](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/index.html)


最近用到了很多需要异步处理的事件。 iOS 集成了几种不同的异步操作，搜索了半天觉得 GCD 这个方式比较适合处理公司业务。 于是抽时间搜了些博客，翻阅了API，准备简单的“罗列”一下 GCD 的几种使用方法。

首先来看一下系统预置了3个代码块：

```objc
static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        //整个app生命周期内只会执行一次，例如 singleton malloc 时可以用到。
    });
```

```objc
dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, /*dispatchQueue : 在哪个队列里创建定时器*/);
    dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, /*intervalInSeconds : 定时器的间隔时间*/ * NSEC_PER_SEC, /*leewayInSeconds : 误差值*/ * NSEC_PER_SEC);
    dispatch_source_set_event_handler(timer, ^{

    });
    dispatch_resume(timer);
```

```objc
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(/*delayInSeconds : 延迟时间*/ * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

    });
```

> 看到了吗？就像 1 + 1 = 2 一样简单！    
> 好吧，好吧，我错了！看不懂没关系，我们拿两个最简单的 GCD 函数来展开研究。

------
* 同步

    `dispatch_sync(dispatch_queue_t queue, ^(void)block);`    
通常在主线程执行队列重的 block。

* 异步

    `dispatch_async(dispatch_queue_t queue, ^(void)block);`    
建议最多不要开辟超过5个线程

----------

这里会遇到一个变量：`queue`(队列)，其类型为 `dispatch_queue_t`。

### 三种常用的队列：

* 取得主队列，通常是 UI 线程。

```objc
dispatch_queue_t mainQueue = dispatch_get_main_queue();
```

* 取得全局队列

> 第一个参数是队列优先级，default, high, low, background.      
> 第二个参数是 `flag`，一般来说传 `0`。理由因为 API 中是这样说的：    
>> Reserved for future use. Passing any value other than zero may result in a `NULL` return value.

```objc
dispatch_queue_t globalQueue =  dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

* 自定义新队列

> 第一个参数是队列的名称，可以为空，注意这里是类型是 `const char *`    
> 第二个参数是队列的属性，简单的需求用不到，传 `NULL` 即可。API 中提供如下解释：    
>> `DISPATCH_QUEUE_SERIAL`, `DISPATCH_QUEUE_CONCURRENT`, or the result of a call to the function `dispatch_queue_attr_make_with_qos_class()`.

```objc
 dispatch_queue_t customQueue = dispatch_queue_create("custom_queue_name", NULL);// const char *
```

### 全部队列形式：

获取主队列：  `dispatch_get_main_queue`    
获取全局队列：`dispatch_get_global_queue`    
新建队列：   `dispatch_queue_create`    
获取当前队列：`dispatch_get_current_queue`   
用QOS创建队列属性： `dispatch_queue_attr_make_with_qos_class`（quality-of-service）    
获取队列名： `dispatch_queue_get_label`    
为目标队列添加对象： `dispatch_set_target_queue`    
执行 block 提交到主线程： `dispatch_main`    

```objc
// 自己创建的队列，使用完成后记得释放内存。—— ARC 不用调用。
dispatch_release(serialQueue1);
dispatch_release(serialQueue2);
```

## 小结：

> 这只是我在工作中能遇到的一些简单的 GCD 函数，如果需要更深入的去了解并发编程，我还有很长的路要走。


