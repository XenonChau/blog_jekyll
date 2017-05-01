---
layout: post
title:  "KVC && KVO"
modified:   2013-08-21 20:17:03 +0800
categories: Objective-C
---

## Overview

> 磕磕绊绊的查阅了官方 API ，加上前辈们的博客片段，我也总结一下我理解的 KVC && KVO。

总所周知，OC 是一门动态语言，KVC / KVO 就是利用 `runtime` 机制动态分配和改变内存。

KVC / KVO 的操作方法分别由 `NSKeyValueCoding` / `NSKeyValueObServing` 协议提供，而 NSObject 实现了这两个非正式协议，也就是说 OC 中几乎所有的对象都支持 KVC && KVO 操作。

## Key-Value Coding

KVC，即是指 [NSKeyValueCoding](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Protocols/NSKeyValueCoding_Protocol/Reference/Reference.html) ，一个非正式的 Protocol，提供一种机制来间接访问对象的属性。KVO 就是基于 KVC 实现的关键技术之一。

KVC 运用了一个 `isa-swizzling` 技术。`isa-swizzling` 就是类型混合指针机制。KVC 主要通过 `isa-swizzling` ，来实现其内部查找定位的。`isa` 指针，如其名称所指（就是"is a"的意思），指向维护分发表的对象的类。该分发表实际上包含了指向实现类中的方法的指针和其它数据。

> 扩展阅读：[《Objective-C内存布局》 - 课蜜黄蜂@博客园](http://www.cnblogs.com/csutanyu/archive/2011/12/12/Objective-C_memory_layout.html)

###  使用方法

`- (void)setValue:(id)value forKey:(NSString *)key;`    
`- (void)setValue:(id)value forKeyPath:(NSString *)keyPath;`    

`- (id)valueForKey:(NSString *)key;`    
`- (id)valueForKeyPath:(NSString *)keyPath;`

其中 `keyPath` 是一种复杂路径，通常不止一层嵌套关系。举个例子来说：    
`key` 的使用方法：`valueForKey:@"admin"` 它通常和accessor方法或是变量同名，并且必须以小写字母开头；    
`keyPath` 的使用方法：`valueForKeyPath:@"school.class.person.name"` 是以“.”分隔的key的集合，因为属性值也能包含属性。

如果访问的 `key` 不存在，则会抛出异常，用于接受异常的方法如下：

`- (void)setValue:(id)value forUndefinedKey:(NSString *)key;`    
`- (id)valueForUndefinedKey:(NSString *)key;`

## Key–Value Observing

KVO，即使指 [NSKeyValueObserving](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Protocols/NSKeyValueObserving_Protocol/index.html) 是Cocoa的一个重要机制，他提供了观察某一属性变化的方法，极大的简化了代码。这种观察－被观察模型适用于这样的情况，比方说根据A（数据类）的某个属性值变化，B（view类）中的某个属性做出相应变化。

当观察者为一个对象的属性进行了注册，被观察对象的 `isa` 指针被修改的时候，`isa` 指针就会指向一个中间类，而不是真实的类。所以 `isa` 指针其实不需要指向实例对象真实的类。所以我们的程序最好不要依赖于 `isa` 指针。在调用类的方法的时候，最好要明确对象实例的类名。

### 使用方法

> **注册**    

```objc
- (void)addObserver:(NSObject *)anObserver
         forKeyPath:(NSString *)keyPath
            options:(NSKeyValueObservingOptions)options
            context:(void *)context;
```    
* `anObserver`：为被观察对象添加的观察者；
* `keyPath`：路径，就是要观察的属性值；
* `options`：选项，给你观察键值变化的选择, 监听新值或者旧值；
* `context`：上下文，用来传输你需要的数据( `void *` 类型瞩目)；

> **监听**

```objc
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary *)change
                        context:(void *)context;
```    
* `keyPath`：路径，注册时添加的属性值；
* `object` ：时刻监听的观察者；
* `change` ：发生的改变值，是新值还是旧值由注册时确定的；
* `context`：上下文，注册时传递的数据；

> **销毁**

```objc
- removeObserver:(NSObject *)anObserver
      forKeyPath:(NSString *)keyPath
         context:(void *)context;
```    
> 当不需要监听的时候，请务必销毁观察者，否则会造成内存泄漏。

## End Words

`Objective-C` 中 `runtime` 机制也是一个非常值得深入研究的东西，希望我能真正的理解其中一部分，届时我会写一篇博客来记录一下的。


