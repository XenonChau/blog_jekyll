---
layout: post
title:  "Python3 学习笔记（三）——函数"
description:
modified:   2016-09-09 20:07:00 +0800
categories: Python
tags: [基础, 入门, 运维, 后端]
---

## Overview

> 书接上文：[《Python3 学习笔记（二）——基本语法篇》]({{site.url}}{{site.baseurl}}/python-step-by-step-2/)

## Day 3 - 学习函数

### Code Sample

Python 除了关键字（keyword）、内置的类型（built-in）和函数（function）, 更多的功能是通过library（即 module[^1] ）来提供的。

引用的语法有如下几种方式：

```python
import math, os                         # 可以使用逗号分隔多个库
from twisted.internet import reactor    # 可以从某个库的某个类中引入
from bs4 import BeautifulSoup as bs     # 可以给引入的库设置别名
```

函数的格式和上一篇提到的 if 判断语法很相似：

```python
def foo(parameters):      # 函数名要写 () 即使参数列表为空, 函数要以 : 结尾。
    ...do some function.  # 函数实现体要有缩进，通常协定是4个空格。
    return result         # 返回 None 可以只写一个 return 或者省略掉它。
    # pass                # 如果要定义一个空函数, 只需要加一个 pass 即可。
```

### 参数

Python 中函数的参数是很有趣的，从类型上分，可分为：位置参数、默认参数、可变参数、关键字参数和命名关键字参数，这些参数又可以通过不同的组合方式来共同使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

#### Positional Argument

当一个函数有一个或多个参数时，这些参数都叫做**位置参数**。调用函数时，传入的值会按照位置顺序依次赋值给函数内部。

#### Default Argument

如果一个函数的位置参数有些是可选的——例如：一个班级里的学生他们的班级参数都是相同的，那么定义函数的时候可以给这个设置为**默认参数**。

```python3
def studentInfo(name, age, gender, classNum='02'):
    stu.name = name
    stu.age = age
    stu.gender = gender
    stu.classNum = classNum
```

跟其他语言一样，默认参数必须要在参数列表的结尾。

**默认参数必须指向不可变对象![^2]**

引用廖老师文章里的例子：

```python
def add_end(L=[]):
    L.append('END')
    return L
```

当你使用默认参数反复调用时，结果就不对了：

```python
>>> add_end()
['END']
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

这是因为 Python 函数在定义的时候，默认参数 `L` 的值就被计算出来了，即 `[]` ，因为默认参数 `L` 也是一个变量，它指向对象 `[]` ，每次调用该函数，如果改变了 `L` 的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的 `[]` 了。要修改上面的例子，我们可以用 `None` 这个不变对象来实现： 

```python
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

#### Varargs (Variable Arguments)

如果我们想构造一个参数数量不固定的函数，可能会想到用一个 list 或者 tuple 作为参数。但是 Python 提供给我们一个不用预先构建 list 的参数类型：**可变参数**。

例如：

```python
def calcNums(nums):
    pass
    
calcNums((1, 2, 3, 4, 5))    # <- 参数是一个 list / tuple 结构。

# 或者组合一个 list / tuple 传过去
nums = (1, 2, 3, 4, 5)
calcNums(nums)
```

就可以简化其调用过程为：

```python
def calcNums(*nums):
    pass

calcNums(1, 2, 3, 4, 5)     # <- 参数像 位置参数 一样用逗号分隔，简洁优雅。

# 也可以在 list / tuple 前面加上 * 传进去
nums = [1, 2, 3, 4, 5]
calcNums(*nums)
```

`*nums` 表示把 `nums` 这个 list 的所有元素作为可变参数传进去。这种写法相当有用，而且很常见。

**可变参数** 的数量可以是<font color=red>0</font>个。

> 请注意： `*` 在 Python 中不代表指针，同样的，接下来要提到的 `**` 也不代表指向指针的指针。

#### Keyword Arguments

同样的，如果函数里包含一个 dict 类型的参数，我们也可以不用预先定义参数类型，将 dict 前面加上 `**` 作为**关键字参数**传递给函数。

```python
def person(name, age, **kw):
    pass

# 此时可以在一个 dict 前面加上 ** 直接传过去。
extraInfo = {"city": "Beijing", "hobby": "football"}
person("John Doe", 42, **extraInfo)

# 亦可像可变参数一样，使用逗号直接传参。
person("Jone Doe", 18, city="Chengdu", hobby="singing")
```

`**kw` 对传入参数进行了拷贝操作，所以对 `**kw` 的改动不会影响 `extraInfo`。

向 `**kw` 中我们可以传入任意关键字的参数，如果我们想限制关键字的名称，就会用到接下来的**命名关键字**。

#### Named Keyword Arguments

如果要限制关键字的名称，只需要简单的在**位置参数**和**命名参数**之间加入一个 `*` 参数来分隔，`*` 后面的参数被视为命名关键字参数。这个 `*` 仅作为分隔符，在函数里不进行任何操作。

```python
def person(name, age, *, city, job):
    print(name, age, city, job)
```

如果一个函数里已经有**可变参数**了，那这个 `*` 就不需要再写了。（**个人理解：** `*` 分隔符可看作是加入了一个匿名的可变参数。）

**命名关键字参数**必须传入参数名，如果没有可变参数则必须设置 `*` 分隔符，不然解释器会按照**位置参数**来解释的：

```python
person('Jack', 24, 'Beijing', 'Get drunk')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: person() takes 2 positional arguments but 4 were given
```

**命名关键字参数**可为每一个关键字设置默认参数，其默认参数不受结尾位置影响。

#### 组合使用

之前我们说过，Python 函数中参数的定义顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

```python
def foo(a, b, c=0, *args, **kw):        # * 分隔符可以看作匿名的可变参数
    print("a=", a, "b=", b, "c=", c, "args="args, "kw=", kw)
    
>>> f1(1, 2, 3, 'a', 'b', x=99)         # 不难看出，按照位置关键字计算结束之后，'a' 和 'b' 都是可变参数，x=99 因为有参数名所以是关键字参数。
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
    
# Python 最神奇的地方在于，你可以使用一个 *args 和一个 **kw 作为参数传递给它，它还能解释出来！
    
>>> args = (1, 2, 3, 4)                 # 定义可变参数
>>> kw = {'d': 99, 'x': '#'}            # 定义关键字参数
>>> foo(*args, **kw)                    # 将神奇组合的参数传入函数调用
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
```

> 所以，对于任意函数，都可以通过类似 `func(*args, **kw)` 的形式调用它，无论它的参数是如何定义的。


## 引用文献：

> [^1]: [《Python常用库整理》@知乎专栏](https://zhuanlan.zhihu.com/p/21563130)
> 
> [^2]: [《函数的参数》@廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431752945034eb82ac80a3e64b9bb4929b16eeed1eb9000)




