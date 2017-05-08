---
layout: post
title: "如何适配iPhone 5的屏幕"
description: "screen resolution"
modified: 2013-07-03 19:47:58 +8000
category: Objective-C
tags: [入门, 教程, OC, iOS]
---

> 看完 WWDC 是有些兴奋，但是随之而来的是一些新问题。 Xcode 5 不熟悉，公司项目好多页面都要适配 iPhone 5 的屏幕，看到了一些大神的解决方案：Auto Layout，感觉自己还没到那个层面，就用我熟悉的方式来解决适配问题吧！

## Launch Screen

在工程中的 `TARGETS` -> `General` 标签下，这个文件一定要设置。

## 用代码判断机型

```objc
if(UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone)
{
    CGSize result = [[UIScreen mainScreen] bounds].size;
    if(result.height == 480)
    {
        // iPhone Classic
    }
    if(result.height == 568)
    {
        // iPhone 5
    }
}
```

出于方便的目的，我们可以在工程中创建一个 `XXGlobal.h` ，然后在其中添加如下宏：

```objc
#define IS_IPAD UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad 
#define IS_IPHONE UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone
#define IS_WIDESCREEN (fabs((double)[[UIScreen mainScreen] bounds].size.height - (double)568) < DBL_EPSILON) 
#define IS_IPHONE_5 (!IS_IPAD && IS_WIDESCREEN)
```

## 使用不同的 Xib 文件

```objc
UIViewController *viewController3;
if ([[UIScreen mainScreen] bounds].size.height == 568)
{
    UIViewController *viewController3 = [[[mainscreenview alloc] initWithNibName:@"iphone5screen" bundle:nil] autorelease];               
}    
else
{
     UIViewController *viewController3 = [[[mainscreenview alloc] initWithNibName:@"iphone4screen" bundle:nil] autorelease];
}
```

## 小结

苹果新出的的 Auto Layout 是用来适配屏幕的，之前的 Autosizing 是用来适配屏幕旋转的，不是一回事！看起来这东西很厉害啊，需要学习一下！！


