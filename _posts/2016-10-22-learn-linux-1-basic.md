---
layout: post
title:  "一点Linux学习心得（基础）"
description:
modified:   2017-05-23 23:37:06 +0800
categories: Linux
tags: [基础]
---

> 阅读了《鸟哥》两部曲，我觉我得对 Linux 能有一个大概的了解了。因为没有项目实战，很多都只是操作过而已，过目就忘。

## 概念

提到 Linux，就不得不提 [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds)。

> 对于 Linux 之父想必大家比我都熟悉他的事迹——我就不再的赘述了。

再说说我们为什么选择 Linux ？

> 即便这里是我自己的博客，我也不想被别人搜索到后对我的文章嗤之以鼻：“这家伙说的只是一面之词，w 和 m 系统比 Linux 在 xx 和 yy 还有 zz 方面都好太多了”。

我有三个笔记本：`MacBook Air`、`Surface 3`、`Dell INS15VD-5106`，出于各种各样的原因，我现在只携带了一个 MBA 和这个笨重的 5106 在外游荡。5106 买到手的时候就是 Ubuntu 系统，我重新分区做了更适合中国人使用的 GUI —— Ubuntu-Kylin（优麒麟）。既能满足我日常休闲的需求（玩玩 steam 上的小游戏），也能使用其 Debian Kernel 进行开发和学习。

> 2017-05-23: 优麒麟在很多软件兼容性上有很大的问题，Win 10 自带的 WSL 也有很多不兼容问题。

说了这么多前奏，还是没提为什么用 Linux ？对，我跑题了，忘了说了。

## 入门基础

Linux 抛去 GUI 部分，其中大部分时间都是在和 Shell 打交道，所以 Shell 命令自然是我学习的主要目的。

几种常见的 Shell：
* Bourne Shell（/usr/bin/sh或/bin/sh）
* Bourne Again Shell（/bin/bash）
* C Shell（/usr/bin/csh）
* K Shell（/usr/bin/ksh）
* Z Shell（/usr/bin/zsh）
* Shell for Root（/sbin/sh）    

> 扩展阅读：

[《bash、dash与zsh shell》 - walkee@图灵社区](http://www.ituring.com.cn/article/1826)    
[《Linux Shell编程入门》 - suyang@博客园](http://www.cnblogs.com/suyang/archive/2008/05/18/1201990.html)

***最近十分流行Zsh，所以一旦有演示截图，均为 Zsh。初级知识不涉及到每个 Shell 之间的区别，所以并不需要在意这些 —— 我只是觉得 `oh-my-zsh` 的 GUI 比较漂亮而已。***

Z Shell(Zsh) 是一种Unix shell，它可以用作为交互式的登录shell，也是一种强大的shell脚本命令解释器。Zsh可以认为是一种Bourne shell的扩展，带有数量庞大的改进，包括一些bash、ksh、tcsh的功能。

zsh的手册里非常有名的第一句“鉴于zsh有许多功能，手册被分为数个章节”，总计有17章节，可见其手册的长度。

||||
-|-|-|-
[Z shell - wikipedia](https://zh.wikipedia.org/zh-hans/Z_shell)|[Z shell homepage](http://www.zsh.org)|[Zsh homepage](http://zshwiki.org/home/)|[oh-my-zsh github](https://github.com/robbyrussell/oh-my-zsh)

#### 语法规则：

```
$<command> [-options] [file]
```    
*其中 `$` 表示终端提示符。*

**常用命令：**

- **`man`：(manual)**    

应该被第一个介绍的命令！    
意思是使用手册 ，所有的命令都可以通过 `man` 来了解每一个命令代表什么含义以及他的用法。    
**eg:** `man cd`    

- **`su` / `sudo`：(super user / super user do)**    

获取超级用户权限，在某些系统目录下、或操作某些关键性文件，需要 root 用户权限。最低限度的保证了系统安全。

- **`cd`：(change directory)**    

切换至某个文件夹下，我们会经常用到该命令。    
**eg:** `$cd /usr/bin/`    

- **`ls`：(list)**    

列出目录下包含的文件，加不同的参数会显示出不同的结果样式。    

- **`grep`:(Global Regular Expression Print)**    

全局正则表达式输出，正则表达式我就不多赘述了。    
**eg:** `$ls -l | grep "^d"`    

**文件操作类：**

>`mkdir`(make directory): `$mkdir <dir_name>`    
`rmdir`(remove directory): `$rmdir <dir_name>`    
`mv`(move): `$mv <old_file_path> <new_file_path>`    
`cp`(copy): `$cp <old_file_path> <new_file_path>`    
`rm`(remove): `rm <file_name> or <dir_name>`    

以上均为文件、目录操作：    
其中 `rmdir` 只能删除**非空**目录。    
`rm` 有两个经典参数 `-r` `-f` 分别代表 `root` 和 `force`。    
经常会看到网传段子：新手 developer 误操作输入了

```
$sudo rm -rf / user/username/removable_dir/*.jpeg
```

由于在 `/user` 这里误输入了一个空格，此命令就变成了删除 `/` 目录下的全部文件。由于 Linux 系统均储存在 `/` 挂载点，所以就相当于删除了整个系统目录。下面会推荐一篇有趣的文章，它并不能帮助你恢复文件，但是可以在清空了公司服务器之后，给自己留个文本签名以示留念。

> 扩展阅读：

[《rm -rf remains》 - Kyle Kelley](http://lambdaops.com/rm-rf-remains/)    
[《rm -rf 的“幸存者”》 - 由`honpey`翻译@伯乐在线 ](http://blog.jobbole.com/70971/)

### Linux 部分 Bash 命令

> 以下文字引自：[《Bash 快捷键》@飘过的小牛](http://github.thinkingbar.com/bash/)

*文章中均为 Linux Bash 命令，Mac 用户可以忽略 Alt 控制键那部分。*

1. 光标移动

    - `Ctrl-A`：将光标定位到本行行首
    - `Ctrl-E`：将光标移动到本行行尾
    - `Ctrl-B`：相当于左箭头键，用于将光标向左移动一格
    - `Ctrl-F`：相当于右箭头键，用于将光标向右移动一格

2. 删除操作

    - `Ctrl-W`：用于删除当前光标左侧的一个单词
    - `Ctrl-K`：用于删除从光标处开始到结尾处的所有字符
    - `Ctrl-U`：用于删除从光标开始到行首的所有字符。一般在密码或命令输入错误时常用
    - `Ctrl-H`：相当于Backspace键，删除光标左侧的一个字符
    - `Ctrl-D`：相当于Delete键，即删除光标所在处的字符

3. 查找类

    - `Ctrl-R`：进入历史命令查找状态，然后你输入几个关键字符，就可以找到你使用过的命令
    - `Ctrl-P`：相当于上箭头键，即显示上一个命令
    - `Ctrl-N`：相当于下箭头键，即显示下一个命令

4. 其他

    - `Ctrl-L`：清屏，相当于clear命令
    - `Ctrl-T`：用于颠倒光标所在处字符和前一个字符的位置
    - `Ctrl-J`：相当于回车键
    - `Alt-C`：于将当前光标处的字符变成大写，同时本光标所在单词的后续字符都变成小写
    - `Alt-L`：于将光标所在单词及所在单词的后续字符都变成小写
    - `Alt-U`：于将光标所在单词的光标所在处及之后的所有字符变成大写


### Mac 用户补充命令

> Mac 系统中 Alt 控制键对应的均为特殊字符。

* Mac 快捷键

    - `⌘-L`：清除当前行至上的一整屏幕文字（不同于Ctrl-L，此为彻底清除。）
    - `⌘-K`：清除当前 tty 全部内容。



#### 文本编辑：

Shell 下还集成了一个强大的文本编辑器 Vi / VIM。

> *Vi 是 Unix 世界里极为普遍的全屏幕文本编辑器， VIM 是它的改进版本 Vi IMproved 的简称。几乎可以说任何一台 Unix 机器都会提供这套软件。*

[必应百科](http://www.bing.com/knows/search?q=vi命令&mkt=zh-cn)

由于这玩意太复杂，以致于我只能进行简单的文本编辑。对还没有抛弃图形 GUI 的我来说简直太难了。

同时 Linux 程序员界也分两大流派：Vi派 和 Emacs派，两个编辑器利用繁多的快捷键，使你的双手不必离开键盘中心区域去寻找“鼠标”、“方向键”、“删除键”，大大的节省了输入中断导致的思维逻辑中断。两大帮派的口水战争持续了数十年，一直没有停息！

> 2017-05-23: Vim 现在已经很熟练的使用了，改天动笔写一篇 Vim 的基础用法。

#### 服务器功能：

《鸟哥的Linux私房菜－－服务器架设篇》，正在反复通读中。

> 2017-05-23: 服务器配置参见 [《阿里云服务器配置日志》](/configure-server-logs)

#### 结语：

Linux 是一个很强大的操作系统，Windows 也是， macOS 也是。

为什么这么说？

`You can you up, no can no bb.`(你行你写一个操作系统玩玩啊？)


