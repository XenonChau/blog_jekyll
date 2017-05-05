---
layout: post
title:  "Python3 学习笔记（一）——环境搭建篇"
description:
modified:   2016-09-07 10:12:00 +0800
categories: Python
tags: [基础, 入门, 运维, 后端]
---

## Overview

> 为什么要学 Python ？
> 
> 玩蛇界的人都知道一句话：“人生苦短，我用 Python 。” (Life is short, use Python!)
> 
> 从技术的角度来说： Python可以写爬虫，开发后端、前端页面（比如豆瓣），能用于运维，能做自动化测试，能写树莓派，可以写游戏（比如：战地2，文明4），能做深度学习、大数据、神经网络……大名鼎鼎的 GAE 也支持 Python。所以根本不用担心学无所用。

## Day 1 - 搭建开发环境

**安装 Python**

**macOS 用户请确认已安装 Homebrew：**  
`brew --version`

```
Homebrew 1.1.5
Homebrew/homebrew-core (git revision 6595; last commit 2016-09-07)
```

安装很简单：  
`$ brew install python3`

**Linux 用户根据内核不同使用相应的包管理工具进行安装即可：**

> Ubuntu:  
`sudo apt-get install python3`

> CentOS:  
`sudo yum install python3`

**Windows 用户，请查阅廖老师的网站[^1]。**

> 是的，我就是懒的写 Windows 环境，你来打我啊？

安装完成后检查一下版本：

`$ python --version` 此为多数系统（不含 Windows）自带的 python 版本。  
`$ python3 --version` 这个是我们刚刚安装的 python，以后使用时要用 `python3` 加以区分。

做到这里环境就算搭建完成了。  
可以用 任何一个 文本编辑器来书写 Python 代码，保存为 xxx.py 即可。  
推荐使用 Vim 编辑器来写，其他小白看着会各种羡慕你的。

## 引用文献：
> [^1]: [《Python 3.0 教程》@廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432170937506ecfb2f6adf8e4757939732f3e32b781c000)


