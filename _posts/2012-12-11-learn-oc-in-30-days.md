---
layout: post
title: "30天学会Objective-C"
description: "Objective-C"
modified: 2012-12-11 11:52:31 +8000
category: Objective-C
tags: [入门, OC, iOS]
---

> 考CCNA也没考上，总干网管也不是个事啊！想着换点啥工作，思来想去还是回归本专业，做软件开发吧。  
> 大学的时候学校里教的是BASIC，后来还有比较新一点的语言叫VisualBasic。不，不是VB.NET，就是VB。大三刚开学的时候学长回来找我们玩，跟我们语重心长的说：现在社会上都用Java了，咱们学的VB没啥用了，赶紧自学Java吧！那时候觉得“我花了几年时间学这么些东西，结果一点用处都没有？”，觉得很伤心，毕业之后玩了半年就在这家公司当IT技术人员，一干就是6年！由于某些<span style="border-bottom:dotted 0.5px #0000cc;" title='简称"准丈母娘"'>具有中国特色的女性长辈</span>的原因，我决定应该作出改变了！思来想去最后决定学习苹果手机应用开发。  

# 学习前的准备

苹果公司真的挺霸道的，想学它们的开发语言给它们赚钱，还必须要用它们的系统才行。我试着用自己的PC装黑苹果（Hacintosh），然而照着威锋网的教程折腾了一个月还是可耻的失败了。反正就是显卡主板CPU网卡等等等等有各种各样的不兼容！

算了算兜里的钱，就买一个MacMini吧！我的GreenHouse显示器正好有两个输入端口，一个是现在的PC，另一个接MacMini！在某宝上找了一个价格不算太贵销量比较正常的一家店铺，下单等快递收货！喔～～我现在是苹果开发者啦！（真表脸！）

MacMini安装了最新的Mountain Lion操作系统，别说这个系统还真有点不适应。使用之前必须先激活，还要先注册一个AppleID！！还有它的鼠标的滚动方式，花键（苹果系统独有的键位，相当于Windows下的Win键。）和alt键的位置跟Windows完全相反。控制面板里面也奇奇怪怪的！总之这东西给我第一印象不是特别好！好在我表姐夫用Mac电脑用了很多年了，他作为一个资深法国程序员，给了我不少操作系统上的指点！（还给了我不少中华烟——不知道他为啥就喜欢抽红塔山，奇怪！哈哈哈哈！——是的，我表姐嫁给法国人了，你有意见吗？）

使用AppStore下载个软件怎么这么难？下载几秒钟就断线，然后需要在已购项目中反复重试。就这样重试啊重试，终于有一次能不断线了——就这样开着电脑一晚上，终于把开发软件Xcode下载下来了！

# Hello World

在书店里能找到的书都是很low的！真的！推荐一个特别牛逼的教程：斯坦福白胡子老头Paul的[《iPhone应用开发》（CS 193P iPhone Application Development）](http://web.stanford.edu/class/cs193p/cgi-bin/drupal/downloads-2011-fall)

{% highlight objective-c %}
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        NSLog(@"Hello, World!");
    }
    return 0;
}
{% endhighlight %}

使用花键+R运行程序，可以得到如下结果：

{% highlight objective-c %}
2012-12-01 13:05:34.967 CalculatorBrain[36390:7162794] Hello, world!
{% endhighlight %}

然后跟着白胡子老头Paul一步一步做了一个计算器，真的很有成就感！

> （2016-10-31更新，图床被日了，图都没有了。）

# 基本语法

Objecitve-C的语法和C语言看起来很不一样，它是基于一种“消息发送”的模式来进行函数调用的。（这边不叫函数，叫方法！）看起来像这样：

{% highlight objective-c %}
[object message];// 意思就是用object这个实例对象来调用message这个方法。
{% endhighlight %}

前面提到过，OC的字符串前面要加一个<code>@</code>，看起来像这样：

{% highlight objective-c %}
@"Hello, world!";
{% endhighlight %}

C语言中的<code>const char *</code>类型的字符串在OC中很少使用。

OC中的数组和C语言中的数组也不一样，这边的数组长这样：

{% highlight objective-c %}
NSArray *array = [NSArray arrayWithObjects:@"Adam", @"Bob", @"Chris", nil];
// 或者
NSArray *array = [[NSArray alloc] initWithObjects:@"Adam", @"Bob", @"Chris", nil];
{% endhighlight %}

这里又要说到初始化方式了：

上面第一种方式叫做遍历构造器，在OC中是一个<code>+</code>方法，又叫“类方法”，是用类名直接调用的方法。第二种是一个<code>-</code>方法，又叫“实例方法”，是用实例变量进行调用的。alloc就会开辟一块内存，生成一个<code>NSArray</code>的实例变量！

在OC中所有的东西都是“对象”，就是object！（除了常量！）

创建完一个对象之后，一定要记得释放内存。和C语言中的delete语句不一样，这个不是直接销毁这块内存，它有一个很重要的规则叫做“引用计数”（Reference Counting），每次这个实例变量被另外一个对象持有的时候，他的retain count就会进行一次<code>+1</code>操作。当该对象的retain count等于0的时候，这个对象才会被销毁。

进行+1操作的语句是：<code>retain</code>，进行-1操作的语句是：<code>release</code>。

有的时候当前类需要开放一个“属性”给别的类来使用，要注意的是写在.h的属性，要在.m中声明一下其成员变量的名字：

{% highlight objective-c %}
// Object.h
@property (nonatomic, assign) int flag;

// Object.m
@synthesize flag;
{% endhighlight %}

propety就是属性，它不仅仅是成员变量，当我们写了synthesize之后，它还会帮我们生成setter和getter方法。需要注意的是OC中的getter方法如果需要重写，请务必不要写成getFlag，<code>- (void)flag;</code>这样就是标准写法了。

# 总结

Objective-C语法奇怪，内存管理很特别，但是也不能阻止我成为一个开发者的决心！30天学会Objective-C那就是吹牛逼，别在意，哈哈哈！

9月新出的iPhone5看起来怪怪的，屏幕改得那么长谁用啊？我还是继续用我的4s～哼哼哼！

（2013-10-31更新：pia pia的打脸啊，我到底还是买了一个日版的iPhone5……卡贴机，除了彩信不能用，其他一切正常！）

