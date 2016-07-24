---
layout: single
title:  "键值观察者(KVO)模式"
date:   2016-08-21 20:17:03 +0520
categories: Objective-C
---

## Key–Value Observing

KVO模式和Target-Action模式很相似，都是通过
Key–value observing, or KVO, is a mechanism somewhat similar to the target–action mechanism, except that it is not limited to controls. (The KVO mechanism is provided through an informal protocol, NSKeyValueObserving, which is actually a set of categories on NSObject and other classes.) The similarity is that objects register with a particular object to be notified when something happens. The “something” is that a certain value in that object is changed.

KVO can be broken down into three stages:

Registration
To hear about a change in a value belonging to object A, object B must be registered with object A.
Change
The change takes place in the value belonging to object A, and it must take place in a special way — a KVO compliant way.
Notification
Object B is notified that the value in object A has changed and can respond as desired.
Here’s a simple complete example — a rather silly example, but sufficient to demonstrate the KVO mechanism in action. We have a class MyClass1; this will be the class of object A. We also have a class MyClass2; this will be the class of object B. Finally, we have code that creates a MyClass1 instance called objectA and a MyClass2 instance called objectB; this code registers objectB to hear about changes in an instance variable of objectA called value, and then changes value, and sure enough, objectB is notified of the change:

// [In MyClass1.h] @interface MyClass1 : NSObject { NSString* value; } @property (nonatomic, copy) NSString* ...
