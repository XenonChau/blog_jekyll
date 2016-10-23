---
layout: single
title:  "objc_msgSend消息传递学习笔记 (消息转发)"
date:   2016-09-02 14：31：00 +0800
categories: Objective-C Digest
---

> 原文：[《objc_msgSend消息传递学习笔记 - 消息转发》@desgard.com](https://desgard.com/objc_msgSend2/)

# objc_msgSend消息传递学习笔记 - 消息转发

> 该文是 [objc_msgSend消息传递学习笔记 - 对象方法消息传递流程]({{{site.url}}{{site.baseurl}}/objective-c/digest/objc-msgSend-1/}) 的基础上继续探究源码，请先阅读上文。

## 消息转发机制(Message Forwarding)

Objective-C 在调用对象方法的时候，是通过消息传递机制来查询且执行方法。
如果想令该类能够理解并执行方法，必须以程序代码实现出对应方法。
但是，在编译期间向类发送了无法解读的消息并不会报错，因为在 runtime 时期可以继续向类添加方法，
所以编译器在编译时还无法确认类中是否已经实现了消息方法。

当对象接受到无法解读的消息后，就会启动消息转发机制，并且我们可以由此过程告诉对象应该如何处理位置消息。

本文的研究目标：当 `Class` 对象的 .h 文件中声明了成员方法，但是没有对其进行实现，来跟踪一下 runtime 的消息转发过程。于是创造一下实验场景：

> 同上一篇文章一样，定义一个自定义 `Class` `DGObject` ，并且声明该 `Class` 中拥有方法 `- (void)test_no_exist;` ，而在 `.m` 文件中不给予实现。在 `main.m` 入口中直接调用该类某实例的 `- (void)test_no_exist;` 方法。

![]({{site.url}}{{site.baseurl}}/images/objc_msgSend/anno_test_method.jpg)
