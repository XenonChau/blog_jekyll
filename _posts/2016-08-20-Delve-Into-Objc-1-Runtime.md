---
layout: post
title: "深入学习 Objective-C (一) —— Runtime"
description: ""
modified: 2016-08-20 09:30:12 +0800
category: [Objective-C]
tags: [理论, OC, iOS]
featured: true
comments: true
---

翻了翻之前的博客，感觉那些都不是我写的……（捂脸

> [30天学会Objecitve-C](/learn-oc-in-30-days)  
> [UITableView的使用方法](/how-to-use-uitableview)  
> [UIScrollView的使用方法](/how-to-use-scrollview)  
> [KVC && KVO](/key-value-observing-pattern)  
> [强大的多线程编程－－GCD](/grand-central-dispatch)  

但是不管如何，这都是我人生中的一部分，我可以对文字进行修改但是我不能删掉它们来否认这个过程。所以从今天开始，我就慢慢总结一下这几年学到的 OC 更深层的一些东西吧。

## 概览

objc 所有的头文件可以在下述目录中找到。

![]({{ site.url }}/images/delve_into_objc/objc-headers.jpeg)


在上图中看到文件名跟 runtime 有关的有这几个：

- `NSObjCRuntime.h`
- `objc-runtime.h`
- `runtime.h`

经查看，NSObjCRuntime.h 是用来定义 32 / 64 位系统下整型内存占用，以及一个推荐初始化的宏: 
`NS_DESIGNATED_INITIALIZER` 
这个在很多类的声明中会经常出现 —— 推荐使用的初始化方式。

所以一般来说，只需要导入头文件 `#import <objc/objc-runtime.h>` ，因为它内部只有两行代码：

```objc
#include <objc/runtime.h>
#include <objc/message.h>
```

而在 runtime.h 中又导入了 objc.h，所以还是要简单谈一下这里所做的事情吧。

## objc

`objc.h` 中定义了 类：`Class` , 对象：`id` , 方法名：`SEL` , 方法实现：`IMP` 等等一系列 OC 构建的基础。

> OC 最大的特点就是 *面向对象编程* 和 *消息发送机制* (`message.h`)。

**我们先来“搞对象”：**

`id` 是结构体 `objc_object` 的指针，其定义如下:

```objc
/// A pointer to an instance of a class.
typedef struct objc_object *id;
```

而 `objc_object` 的中的 `isa` 指针就是我们要重点记住的东西了。

```objc
/// Represents an instance of a class.
struct objc_object {
    Class isa  OBJC_ISA_AVAILABILITY;
};
```

`isa` 就是 “is a” 的意思，接下来有关 **[类的定义](#class-definition)** 的时候会重点讲述一下这个指针的。

## runtime

一开始，`runtime.h` 就给我们展示了 4 个重要的结构体指针：

- 方法: `Method`

```objc
/// An opaque type that represents a method in a class definition.
typedef struct objc_method *Method;

struct objc_method {
    SEL method_name                                          OBJC2_UNAVAILABLE;
    char *method_types                                       OBJC2_UNAVAILABLE;
    IMP method_imp                                           OBJC2_UNAVAILABLE;
}                                                            OBJC2_UNAVAILABLE;
```

- 实例变量: `Ivar`

```objc
/// An opaque type that represents an instance variable.
typedef struct objc_ivar *Ivar;

struct objc_ivar {
    char *ivar_name                                          OBJC2_UNAVAILABLE;
    char *ivar_type                                          OBJC2_UNAVAILABLE;
    int ivar_offset                                          OBJC2_UNAVAILABLE;
#ifdef __LP64__
    int space                                                OBJC2_UNAVAILABLE;
#endif
}                                                            OBJC2_UNAVAILABLE;
```

- 分类: `Category`

```objc
/// An opaque type that represents a category.
typedef struct objc_category *Category;

struct objc_category {
    char *category_name                                      OBJC2_UNAVAILABLE;
    char *class_name                                         OBJC2_UNAVAILABLE;
    struct objc_method_list *instance_methods                OBJC2_UNAVAILABLE;
    struct objc_method_list *class_methods                   OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
}                                                            OBJC2_UNAVAILABLE;
```

- 属性: `objc_property_t`

```objc
/// An opaque type that represents an Objective-C declared property.
typedef struct objc_property *objc_property_t;

// 在本类中未定义其结构体内容
// 相关方法：
objc_property_t class_getProperty(Class cls, const char *name)
```

这里我们来举个栗子：

比如我们在项目中使用开源库 [MJExtension](https://github.com/CoderMJLee/MJExtension) 来解析 model ，有时后台返回的 JSON 数据中包含了 `id` 字段。而 `id` 又是 OC 的系统保留关键字，所以一般的做法是将 `id` 替换成 `ID`：

```objc
[XXXModel mj_setupReplacedKeyFromPropertyName:^NSDictionary *{
                return @{
                         @"ID": @"id",
                         };
            }];
```

*每一个请求回来的数据都要这么写，不累么？*

根据 OC 的动态特性，我们可以在 BaseModel 中判断子类中是否声明了 `ID` 的属性，并将其统一由上述方法来进行替换，代码如下：

```objc
Class currentClass = object_getClass(self);
if (class_getProperty(currentClass, "ID")) {
    [currentClass mj_setupReplacedKeyFromPropertyName:^NSDictionary *{
        return @{
            @"ID" : @"id",
            };
    }];
}
```

这样，你的 model 中就再也不用去考虑 `id` 转换的问题了。

接下来 `runtime.h` 中提到的是有关**类**、**协议**、**方法**以及**属性内部特征**的定义，以及其相关的操作。这里着重讲一下 **类** 和 **方法** 吧。

### Class Definition

首先看源码：

```objc
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
/* Use `Class` instead of `struct objc_class *` */

#endif
```

请注意这里有一个 `#if !__OBJC2__` 运行时宏。即是说 Apple 在 OC 2.0 的时候把类的定义藏起来了，但是我们还是可以通过之前的结构体来分析一下类的内存分布情况。

#### super_class: 

父类，又称超类，是指向该类继承关系中上一级的类的指针。    

```objc
OBJC_EXPORT void objc_msgSendSuper(void /* struct objc_super *super, SEL op, ... */ )

/// Specifies the superclass of an instance. 
struct objc_super {
    /// Specifies an instance of a class.
    __unsafe_unretained id receiver;

    /// Specifies the particular superclass of the instance to message. 
#if !defined(__cplusplus)  &&  !__OBJC2__
    /* For compatibility with old objc-runtime.h header */
    __unsafe_unretained Class class;
#else
    __unsafe_unretained Class super_class;
#endif
    /* super_class is the first class to search */
};
```

当使用 `super` 调用方法时，runtime 会去调用 `objc_msgSendSuper()`。

#### ivars: 

指向 *成员变量* 列表数组的指针。

#### methodLists: 

指向 *方法列表* 数组指针的指针。

**方法列表**在头文件最后也有给出来定义，跟 `Class` 一样，也在 OC 2.0 中隐藏了其实现原理。

```objc
struct objc_method_list;

struct objc_method_list {
    struct objc_method_list *obsolete                        OBJC2_UNAVAILABLE;

    int method_count                                         OBJC2_UNAVAILABLE;
#ifdef __LP64__
    int space                                                OBJC2_UNAVAILABLE;
#endif
    /* variable length structure */
    struct objc_method method_list[1]                        OBJC2_UNAVAILABLE;
}                                                            OBJC2_UNAVAILABLE;
```

**\*个人猜测\***：因为这个指针 `**methodLists` 是指向了方法列表指针的指针，所以这个在 `Class` 内存分配结束之后，是可以被修改的。这也是 `Category` 中只可以添加方法而不能添加属性的理由。而 `Extension` 的实现原理是需要在 `@implementation` 中进行实现的，它重新分配了 `Class` 的内存布局。

![](../../images/delve_into_objc/class-extension-declaration.jpeg)
<div style="text-align:center;margin:auto;color='#CCCCCC'"><i>书写 <code>@interface</code> 时的代码提示。</i></div>

### isa

要着重说明的就是这个 `isa` 指针了。先看图：

![](../../images/delve_into_objc/isa_ptr_relation.png)  
*图片来源：[http://www.cnblogs.com/](http://www.cnblogs.com/csutanyu/archive/2011/12/12/Objective-C_memory_layout.html)*

根据官方文档所述：
>Every object is connected to the run-time system through its isa instance variable, inherited from the NSObject class. isa identifies the object's class; it points to a structure that's compiled from the class definition. Through isa, an object can find whatever information it needs at run time, such as its place in the inheritance hierarchy, the size and structure of its instance variables, and the location of the method implementations it can perform in response to messages.

- ***类对象*** 中的 `isa` 指向其元类 `meta_class`
    - `meta_class` 存储类的 ***静态变量*** 与 ***类方法*** (`+`方法)
- ***实例对象*** 中的 `isa` 指向其所在的类 `class`
    - `class` 存储类的 ***成员变量*** 与 ***实例方法*** (`-`方法)
- OC 中所有的对象都继承自 `NSObject`, 它是所有类的 ***根类*** (`root_class`)
- `root_class` 中 ***元类对象*** 的 `isa` 指向其本身

`isa` 指针是了解 OC 类非常重要的一环，但是永远不要主动去调用它。

```objc
if (self->isa == [self class]) {
    NSLog(@"It's True");
}
```

在 64-bits SDK 环境下，IDE 会提示你去修正这个错误：

![](../../images/delve_into_objc/self->isa.jpeg)

> Reference:
> 
> [\[objc explain\] Non-pointer isa](http://www.sealiesoftware.com/blog/archive/2013/09/24/objc_explain_Non-pointer_isa.html) - *Greg Parker*
> 
> [\[Cocoa\] 深入浅出 Cocoa 之类与对象](http://www.cppblog.com/kesalin/archive/2012/01/19/objc_class_object.html) - *罗朝辉（飘飘白云）*
> 
> [\[Objective-C 内存布局\]](http://www.cnblogs.com/csutanyu/archive/2011/12/12/Objective-C_memory_layout.html) - *课蜜黄蜂*

## 应用场景：

### Associated Objects

**对象关联**最重要的一个作用就是：在 Category 中添加属性！

这是它的三个相关方法：
```objc
objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy)
objc_getAssociatedObject(id object, const void *key)
objc_removeAssociatedObjects(id object)
```

看方法名就知道其作用了，那么我们来尝试着使用一下，向 `NSObject` 中添加一个 `someProperty` 属性：

```objc
// NSObject+SomeProperty.h
@interface NSObject (SomeProperty)
@property (copy, nonatomic) NSString *someProperty;
@end
```

```objc
// NSObject+SomeProperty.m
#import "NSObject+SomeProperty.m"
#import <objc/runtime.h>

@implementation NSObject (someProperty)
@dynamic someProperty;

- (void)setSomeProperty:(NSString *)newProperty {
     objc_setAssociatedObject(self, 
                              @selecto(someProperty), // const void *key
                              newProperty, 
                              OBJC_ASSOCIATION_COPY_NONATOMIC //objc_AssociationPolicy policy
                              ); 
}

- (NSString *)someProperty {
    return objc_getAssociatedObject(self, _cmd);
}
@end
```

- 如果需要定义 枚举型 (NS_ENUM aka.NSInteger)，请使用 `NSNumber` 类型进行操作。

```objc
objc_setAssociatedObject(self, 
                         @selector(someEnum),
                         @(someEnum),
                         OBJC_ASSOCIATION_RETAIN_NONATOMIC);
// 这里虽然是 int 类型的结构，但是我们用的 @(someEnum) 它是一个对象，需要对其进行 retain 操作。不然会出现野指针的情况。
```

- 如果需要定义 结构体 (struct)，请使用 `(__bridge 'structType')` 进行类型转换。

```objc
objc_setAssociatedObject(self, 
                         @selector(someStruct),
                         (_bridge 'structType')someStruct,
                         OBJC_ASSOCIATION_ASSIGN);
```

- `_cmd`: 它代表当前方法的 `selector`，正如同 `self` 表示当前方法调用的对象实例。setter 方法只需要调用 `@selector(getter)` 即可。

> 关于 `key` 的要求通常推荐是 `static`、 `const char *`，指针类型的当然更好了。只要是确保它是**不变的**、**唯一的**、而且在 setter / getter 内能被访问到的 —— 使用 `_cmd` 是一个最简便的方法了。

有关 

- `OBJC_ASSOCIATION_ASSIGN_NONATOMIC`: 是枚举 `objc_AssociationPolicy` 值，代表设置 `@property` 时括号里对应的内容。

|Behavior|@property Equivalent|
|----------|-----------|
|`OBJC_ASSOCIATION_ASSIGN`|`@property (assign)` or <br/>`@property (unsafe_unretained)`|
|`OBJC_ASSOCIATION_RETAIN_NONATOMIC`|`@property (nonatomic, strong)`|
|`OBJC_ASSOCIATION_COPY_NONATOMIC`|`@property (nonatomic, copy)`|
|`OBJC_ASSOCIATION_RETAIN`|`@property (atomic, strong)`|
|`OBJC_ASSOCIATION_COPY`|`@property (atomic, copy)`|


> Reference:  
> [\[Associated Objects\]](http://nshipster.com/associated-objects/) - *Mattt Thompson*    
> [\[Fun With objc_setAssociatedObject and UIAlertView\]](http://www.wezm.net/technical/2011/12/fun-with-objc-setassociatedobject-and-uialertview/) - *WezM<sup>.net</sup>*    
> [\[ObjC中_cmd的用法\]](http://www.cnblogs.com/mantou811/p/6688560.html) - *botherbox*

### Create Dynamic Classes

没有在项目中找到合适的例子，就用 Stack Overflow 上的回答来举例说明一下吧：

**NSObject+Subclass.h**

```objc
#import <objc/runtime.h>

typedef struct selBlockPair { 
    SEL aSEL; 
    id (^__unsafe_unretained aBlock)(id, ...); 
} selBlockPair;

#define NIL_PAIR ((struct selBlockPair) { 0, 0 })
#define PAIR_LIST (struct selBlockPair [])
#define BLOCK_CAST (id (^)(id, ...))

@interface NSObject (subclass)

+(Class) newSubclassNamed:(NSString *) name
            protocols:(Protocol **) protos
                 impls:(selBlockPair *) impls;

@end
```

**NSObject+Subclass.m**

```objc
@implementation NSObject (subclass)

+(Class) newSubclassNamed:(NSString *)name
             protocols:(Protocol **)protos
                 impls:(selBlockPair *)impls
{
    if (name == nil)
    {
        // basically create a random name
        name = [NSString stringWithFormat:@"%s_%i_%i", class_getName(self), arc4random(), arc4random()];
    }

    // allocated a new class as a subclass of self (so I could use this on a NSArray if I wanted)
    Class newClass = objc_allocateClassPair(self, [name UTF8String], 0);

    // add all of the protocols untill we hit null
    while (protos && *protos != NULL)
    {
        class_addProtocol(newClass, *protos);
        protos++;
    }

    // add all the impls till we hit null
    while (impls && impls->aSEL)
    {
        class_addMethod(newClass, impls->aSEL, imp_implementationWithBlock(impls->aBlock), "@@:*");
        impls++;
    }

    // register our class pair
    objc_registerClassPair(newClass);

    return newClass;
}

@end
```

使用方法如下：

```objc
int main() {
    @autoreleasepool {
        __strong Class newClass = [NSString newSubclassNamed:@"MyCustomString" protocols:NULL impls: PAIR_LIST {
            @selector(description),
            BLOCK_CAST ^id (id self) {
                return @"testing";
            },
            NIL_PAIR
        }];

        NSString *someString = [newClass new];
        NSLog(@"%@", someString);
    }
    return 0;
}
```

输出结果如下：

```objc
2012-10-01 10:07:33.609 TestProj[54428:303] testing
```

> [\[Create custom dynamic classes in Objective-C\]](http://stackoverflow.com/a/12674557) - *James Paolantonio*

### Message Forwarding

提到 *消息转发*，就不得不引入 `message.h` 来一起说一下 *发送消息* 了。

OC 主要两个 *发送消息* 的方法如下：

```objc
/** 
 * Sends a message with a simple return value to an instance of a class.
 * @param self A pointer to the instance of the class that is to receive the message.
 * @param op The selector of the method that handles the message.
 * @param ... 
 *   A variable argument list containing the arguments to the method.
 * 
 * @return The return value of the method.
 */
id objc_msgSend(id self, SEL op, ...)
id objc_msgSendSuper(struct objc_super *super, SEL op, ...)
```

而 *消息转发* 的方法是这样的：

```objc
/* Message Forwarding Primitives
 * Use these functions to forward a message as if the receiver did not 
 * respond to it. 
 *
 * class_getMethodImplementation() may return (IMP)_objc_msgForward.
 */
id _objc_msgForward(id receiver, SEL sel, ...)
```

> 09-02 增补两篇转载文章:  
> [objc_msgSend消息传递学习笔记 (对象方法消息传递流程)](/objc-msgSend-1)  
> [objc_msgSend消息传递学习笔记 (消息转发)](/objc-msgSend-2)  
> 文中的剖析比我看了3天源码要透彻得多得多得多。

[\[Objective-C Runtime Programming Guide: Message Forwarding\]](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html) - *Apple Developer Official Document*

### Method Swizzling

项目中有个需求：在 UITextField 加个默认的协议实现。（比如限制输入文字的长度默认不能超过 11 位）

我的想法是在 Category 中重写一个自己的协议方法，通过 Method Swizzling 进行调换。

首先实现自己的协议方法：

```objc
- (BOOL)xc_textField:(UITextField *)textField 
        shouldChangeCharactersInRange:(NSRange)range 
        replacementString:(NSString *)string {
        
    if (!range.length && textField.text.length >= 11) {
        return NO;
    }
    
    return [self xc_textField:textField 
                 shouldChangeCharactersInRange:range 
                 replacementString:string];
}
```

因为只有当设置代理时才会走协议方法，我们这里需要重写一下 `delegate` 的 setter 方法：

为了方便起见，我们先封装两个用于方法交换的方法：

```objc
+ (void)swizzleWithOriginClass:(Class)originCls
              originalSelector:(SEL)originalSelector
                 swizzledClass:(Class)swizzledCls
              swizzledSelector:(SEL)swizzledSelector {
    
    Method originalMethod = class_getInstanceMethod(originCls, originalSelector);
    Method swizzledMethod = class_getInstanceMethod(swizzledCls, swizzledSelector);
    
    BOOL didAddMethod =
    class_addMethod(originCls,
                    swizzledSelector,
                    method_getImplementation(originalMethod),
                    method_getTypeEncoding(originalMethod));
    
    if (didAddMethod) {
        method_exchangeImplementations(originalMethod, swizzledMethod);
    }
    
}

+ (void)swizzleWithClass:(Class)cls
        originalSelector:(SEL)originalSelector
        swizzledSelector:(SEL)swizzledSelector {
    
    Method originalMethod = class_getInstanceMethod(cls, originalSelector);
    Method swizzledMethod = class_getInstanceMethod(cls, swizzledSelector);
    
    BOOL didAddMethod =
    class_addMethod(cls,
                    originalSelector,
                    method_getImplementation(swizzledMethod),
                    method_getTypeEncoding(swizzledMethod));
    
    if (didAddMethod) {
        class_replaceMethod(cls,
                            swizzledSelector,
                            method_getImplementation(originalMethod),
                            method_getTypeEncoding(originalMethod));
    } else {
        method_exchangeImplementations(originalMethod, swizzledMethod);
    }
}
```

写一下自己的 `delegate` 的 setter：

```objc
- (void)xc_setDelegate:(id <UITableViewDelegate>)delegate {
    [self xc_setDelegate:delegate];
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        [[self class] swizzleWithOriginClass:delegate.class
                            originalSelector:@selector(textField:
                                                       shouldChangeCharactersInRange:
                                                       replacementString:)
                               swizzledClass:self.class
                            swizzledSelector:@selector(xc_textField:
                                                       shouldChangeCharactersInRange:
                                                       replacementString:)];
    });
}
```

然后在 `+load` 中更换 `setDelegate` 和我们自己的 `xc_setDelegate`：

```objc
+ (void)load {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        [self swizzleWithClass:self
              originalSelector:@selector(setDelegate:)
              swizzledSelector:@selector(xc_setDelegate:)];
    });
}
```

当 `[someTextField setDelegate:self];` 的时候，就会调用 `xc_setDelegate` 方法。

同理，协议方法也会被替换成我们自己写的方法。

至此，就实现了给 UITextField 增加默认的输入文字位数限制的方法。

[\[Method Swizzling\]](http://nshipster.com/method-swizzling/) - *Mattt Thompson*
[\[What are the Dangers of Method Swizzling in Objective C?\]](http://stackoverflow.com/a/8636521) - *wbyoung*



