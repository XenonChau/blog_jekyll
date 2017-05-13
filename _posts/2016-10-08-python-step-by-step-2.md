---
layout: post
title:  "Python3 学习笔记（二）——基本语法篇"
description:
modified:   2016-10-08 19:07:00 +0800
categories: Python
tags: [基础, 入门, 运维, 后端]
comments: true
---

## Overview

> 书接上文：[《Python3 学习笔记（一）——环境搭建篇》]({{site.url}}{{site.baseurl}}/python-step-by-step-1/)

## Day 2 - 学习基本语法

### Code Sample

Python的语法比较简单，采用缩进方式，写出来的代码就像下面的样子：

```python
# print absolute value of an integer:
a = 100
if a >= 0:
    print(a)
else:
    print(-a)
```

以 `#` 开头的语句是注释，注释是给人看的，可以是任意内容，解释器会忽略掉注释。其他每一行都是一个语句，当语句以冒号 `:` 结尾时，缩进的语句视为代码块。  
缩进有利有弊。好处是强迫你写出格式化的代码，但没有规定缩进是几个空格还是Tab。按照约定俗成的管理，应该始终坚持使用***`4个空格`***的缩进。  
缩进的另一个好处是强迫你写出缩进较少的代码，你会倾向于把一段很长的代码拆分成若干函数，从而得到缩进较少的代码。  
缩进的坏处就是“复制－粘贴”功能失效了，这是最坑爹的地方。当你重构代码时，粘贴过去的代码必须重新检查缩进是否正确。此外，IDE 很难像格式化 Java 代码那样格式化 Python 代码。  
最后，请务必注意，Python 程序是***`大小写敏感`***的，如果写错了大小写，程序会报错。

### 基础类型和变量

#### 整型: 

`1234`  
`-5050`  
`0`  
`0xcafebabe`

#### 浮点型: 

`1.234`  
`-9.01`  
`1.2e-5`

> 整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（除法难道也是精确的？是的！），而浮点数运算则可能会有四舍五入的误差。 [^1]
> 
> 理由：Python 提供了“地板除” `//` 运算符。

#### 字符串: 

`'hello'`  
`"world"`

#### 布尔值: 

布尔值和布尔代数的表示完全一致，一个布尔值只有 `True`、`False` 两种值，要么是 `True`，要么是 `False` ，在 Python 中，可以直接用 `True`、`False` 表示布尔值（请注意大小写），也可以通过布尔运算计算出来：

```python
>>> True
True
>>> False
False
>>> 3 > 2
True
>>> 3 > 5
False
```

布尔值可以用 `and`、`or` 和 `not` 运算。

#### 空值: 

空值是 Python 里一个特殊的值，用 `None` 表示。`None` 不能理解为 `0`，因为 `0` 是有意义的，而 `None` 是一个特殊的空值。

### 数组

#### List: 可变数组

`classmates = ['Michael', 'Bob', 'Tracy']`  

#### Tuple: 不可变数组

`classmates = ('Michael', 'Bob', 'Tracy')`  

### 条件判断

写一下就明白了, 注意不要漏写了每一个 `:`

```python
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```

### 循环

#### For

```python
sum = 0
for x in range(101):
    sum = sum + x
print(sum)
```

#### While

```python
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

### 容器

#### Dict

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```

如果key不存在，dict就会报错：

```python
>>> d['Thomas']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Thomas'
```

要避免key不存在的错误，有两种办法，一是通过in判断key是否存在：

```python
>>> 'Thomas' in d
False
```

二是通过dict提供的get方法，如果key不存在，可以返回None，或者自己指定的value：

```python
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

注意：返回None的时候Python的交互式命令行不显示结果。

要删除一个key，用pop(key)方法，对应的value也会从dict中删除：

```python
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```

#### Set

`set` 和 `dict` 类似，也是一组 `key` 的集合，但不存储 `value`。由于 `key` 不能重复，所以，在 `set` 中，没有重复的 `key`。`set` 和 `dict` 的唯一区别仅在于没有存储对应的 `value`，但是，`set` 的原理和 `dict` 一样，所以，同样不可以放入可变对象，因为无法判断两个可变对象是否相等，也就无法保证 `set` 内部“不会有重复元素”。试试把 `list` 放入 `set` ，看看是否会报错？

要创建一个 `set` 需要提供一个 `list` 作为输入集合：

```python
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```

通过 `add(key)` 方法可以添加元素到 `set` 中，可以重复添加，但不会有效果：

```python
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

通过 `remove(key)` 方法可以删除元素：

```python
>>> s.remove(4)
>>> s
{1, 2, 3}
```

`set` 可以看成数学意义上的无序和无重复元素的集合，因此，两个 `set` 可以做数学意义上的交集、并集等操作：

```python
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```

## 引用文献：

> [^1]: [《Python 3.0 教程》@廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432170937506ecfb2f6adf8e4757939732f3e32b781c000)

