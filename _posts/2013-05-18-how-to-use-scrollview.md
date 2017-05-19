---
layout: post
title: "UIScrollView的使用方法"
description: "UIScrollView"
modified: 2012-05-18 21:17:03 +8000
category: Objective-C
tags: [入门, 教程, OC, iOS]
---

> 序：上一次我们提到过UITableView，通过查看头文件发现它是继承于UIScrollView的子类，为了更好的深入研究，我决定一层层的向下深入研究。

## 用协议方法来设置ScrollView的各种属性

UIScrollView如其名，是一个可以滚动的控件，可以存放一些超出屏幕范围的内容。一般来说我们可以用它来做相册一类的东西。

初始化方式我就不写了，类似锁定滚动方向、显示滚动条、拖拽回弹等等这些看看头文件就能知道的东西就不赘述了。我主要阐述一下每个协议方法的作用吧：

{% highlight objc %}
#pragma mark - UIScrollViewDelegate
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    // 只要有任何content offset，就会进入这个方法。
}
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView {
    // 手指开始拖拽时进入这个方法。
}
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate {
    // 手指停止拖拽并离开屏幕的时候，进入这个方法。
}
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView {
    // 当滚动即将结束，进入减速阶段，进入这个方法。
}
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
    // 滚动结束，进入这个方法。
}
- (void)scrollViewDidEndZooming:(UIScrollView *)scrollView withView:(UIView *)view atScale:(float)scale {
    // scrollview子view缩放时进入这个方法。
}
{% endhighlight %}

## 案例

项目中遇到了一个小问题：我们的Gallery也要和系统相册一样支持浏览和缩放——这个缩放就难到我一阵子了：

我设置的是每个imageview都是直接add到scrollview上的，缩放的时候就会让contentsize也随着改变。查了一下是scrollview的contentsize是根据其subviews的frame动态变化的。

既然知道了原理那就好改了——我在每个imageview外面又套了一层scrollview，这样既不影响最外层的contentsize，每个imageview也能进行旋转缩放操作。

## 总结

UIScrollView是一个很“特殊”的view，它能给iPhone小小的屏幕增加更多显示区域。
上次提到的UITableView的reuseable cell，最近还在查阅文档，以后会详细介绍一下的。


