---
layout: post
title: "深入学习 Objective-C (三) —— Hash Table"
description: ""
modified: 2016-08-22 18:58:35 +0800
category: [Objective-C, articles]
tags: [理论, OC, iOS]
featured: false
---

> 本文是系列学习笔记第三篇，回顾前文请查阅：
> 
> [深入学习 Objective-C (一) —— Runtime](/Delve-Into-Objc-1-Runtime)
> 
> [深入学习 Objective-C (二) —— Object](/Delve-Into-Objc-1-Object)

# 概览

Objective-C 中有关 `NSDictionary` 等容器的实现，是交由 **哈希表** 又称 **散列表** (Hash Table[^1]) 来实现的。

# Hash 算法

```objc
// Errrrrr... ...

// Text has been deleted.
```

# OC 中的 Hash Table

```objc
// Errrrrr... ...

// Text has been deleted.
```

## Hash Table Open Source

- [`hashtable2.h`](https://opensource.apple.com/source/objc4/objc4-493.9/runtime/hashtable2.h.auto.html)
- <del>[`hashtable2.m`](https://opensource.apple.com/source/objc4/objc4-493.9/runtime/hashtable2.m.auto.html)</del>  (此为2016年6月以前版本)
- [`hashtable2.mm`](https://opensource.apple.com/source/objc4/objc4-709/runtime/hashtable2.mm.auto.html)(2017年之后 hash table 改用 c++ 编写了，请查阅最新版！)

从去年夏末开始，一直到今年5月份，陆陆续续的投简历、面试、研究 OC 底层实现、学习其他领域新知识……

到现在我也写不出来有关**哈希**的博客，看了一眼自己码的字，不堪入目。

把文章发给好友们点评，回复无一不是：“太浅了”、“思维混乱”、“重学一下算法吧”……不堪受辱！于是删了自己写的文字！（大笑以掩饰尴尬

当时写这个博客的时候是不是有点太自大了？留给我的时间并不多，但是我还是不要浮躁，继续努力吧！这篇博文在未来的某个时间内我会重新开一篇的。（届时会把这篇文章的标题修改掉！

[^1]: [Wikipeida : Hash table](https://en.wikipedia.org/wiki/Hash_table#Separate_chaining_with_linked_lists)  |  [维基百科：散列表](https://zh.wikipedia.org/wiki/哈希表)


