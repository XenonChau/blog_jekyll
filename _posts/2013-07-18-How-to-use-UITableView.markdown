---
layout: single
title:  "UITableView的使用方法"
date:   2013-07-18 21:11:31 +0600
categories: Objective-C
---

> 序：看了许多教程，觉得TableView是iOS系统里非常常用的控件。它的cell复用机制可以有效的节约内存。

## 初始化(Initialization)

{% highlight objc %}
@interface MainViewController () <UITableViewDelegate, UITableViewDataSource>

/**
 *  @param nonatomic: 非原子性，不具有线程安全，但是访问速度更快。非多线程情况下优先使用这个定义。
 *  @param retain: 不开辟新的内存空间，其它对象只会生成一个指针，然后指向同一块内存地址，并给引用计数器+1。
 */
@property (nonatomic, retain) UITableView *tableView;

@end
{% endhighlight %}

## 用协议方法来设置TableView的各种属性
{% highlight objc %} 
- (void)viewDidLoad {
    [super viewDidLoad];
	/**
 	 *  @param UITableViewStyleGrouped: 像通讯录一样，组之间会被分隔开来。 UITableViewStylePlain:组与组之间是连贯的。
 	 */
    _tableView = [[[UITableView alloc] initWithFrame:CGRectMake(0, 0, 320, 568) style:UITableViewStylePlain] autorelease];
    [_tableView setDataSource:self];// 指定数据源为controller，写这个可以走跟数据相关的协议方法。
    [_tableView setDelegate:self];// 指定代理为controller，写这个可以走跟样式相关的协议方法。
    [_tableView setTableHeaderView:[UIView new]];// 设置tableview的页眉，跟tableview一起滚动，有别于协议方法里的headerview。
    [_tableView setTableFooterView:[UIView new]];// 设置tableview的页脚，如果页面太短没有填充满屏幕，可以不显示tableview的单元格。
    [_tableView setSeparatorStyle:UITableViewCellSeparatorStyleNone];// 可以隐藏单元格底部的分割线。
    [[self view] addSubview:_tableView];
}

#pragma mark - UITableViewDelegate
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    [tableView deselectRowAtIndexPath:indexPath animated:YES];
    // 这里设置每个单元格的点击事件。
}

#pragma mark - UITableViewDataSource
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    // 返回组数量
    return 2;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    // 返回每组多少
    return 3;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    NSString *cellID = @"cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellID];
    if (!cell) {
        cell = [[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:cellID] autorelease];
    }
    // 设置标题文字
    [[cell textLabel] setText:[NSString stringWithFormat:@"第%zi组", [indexPath section]]];
    // 设置副标题文字
    [[cell detailTextLabel] setText:[NSString stringWithFormat:@"第%zi条", [indexPath row]]];
    // 设置左侧的图像。
    [[cell imageView] setImage:[UIImage imageNamed:@"cell_image.png"]];
    // 右边的指示器，可以有几种模式。
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];
    return cell;
}

#pragma mark - TableView Method
- (UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section {
    // 每组的组头，在下一组的组头过来之前不会滚出屏幕，会悬停在屏幕顶部。
    return [[UIView new] autorelease];
}

- (UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section {
    // 每组的组脚
    return [[UIView new] autorelease];
}
{% endhighlight %}

## 小结

> 本文就是简单的记录一下如何创建一个基本的TableView。

1. 单元格可以根据不同的标识符来使用不同的复用方式。
2. 系统会自动控制滚出屏幕的单元格，滚动速度特别快的时候，来不及复用的时候，会生成很多单元格，并不会闪退。
3. 看API里还有一些像移动、删除列表的方法还有待研究。
4. 看有些应用里会有插入和删除的动画效果，感觉很炫，还是要多看看Github学习一下的。
