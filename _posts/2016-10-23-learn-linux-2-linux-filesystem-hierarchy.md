---
layout: single
title:  "Linux 目录结构"
date:   2016-10-23 21:51:30 +0800
categories: Linux
---

# Linux 目录结构 (Linux Filesystem Hierarchy)

> 说在最前面：Linux 系统中所有的东西都是文件。

## 1.0. /
Linux 的根目录，全部文件都存放在这个目录下。下列目录是系统必须的目录：

![](http://www.tldp.org/LDP/intro-linux/html/images/FS-layout.png)
*图片来源：http://www.tldp.org/LDP/intro-linux/html/sect_03_01.html*


```text
/bin	Essential command binaries
/boot	Static files of the boot loader
/dev	Device files
/etc	Host-specific system configuration
/lib	Essential shared libraries and kernel modules
/media	Mount point for removable media
/mnt	Mount point for mounting a filesystem temporarily
/opt	Add-on application software packages
/sbin	Essential system binaries
/srv	Data for services provided by this system
/tmp	Temporary files
/usr	Secondary files
/var	Variable data
```



## 1.1. /bin
Binary：存放系统二进制文件的目录。默认权限 `777` 。

## 1.2. /boot
引导启动目录，其中最重要的一个文件就是 `vmlinux.<version_code>.i386`。操作系统启动第一个加载的即此文件。

### 1.2.1. /boot/grub
引导程序

## 1.3. /dev
Device，存放所有硬件设备的目录。    
* eg0: /dev/sda 是硬盘， sda1就是硬盘的第一个分区。    
* eg1: /dev/tty tty0 tty1 ... 就是我们的终端设备。    

## 1.4. /etc
et cetera，用于存放系统的配置文件。大多数都是 `.cnf` 或 `.conf`  类型的纯文本文件。

## 1.5. /home
存放所有用户的私有数据，如桌面、文档等……
在每个用户的目录下均存在以下目录：

```text
Desktop		桌面
Documents	文档
Downloads	下载
Music		音乐
Pictures	图片
Public		公共
Templates	模板
Videos		视频
```

### 1.5.0. ~
实际上就是 `/home/$USER/` 目录，当前用户的默认目录。

## 1.6. /lib
Library，存放运行相关的库文件。

## 1.7. /media
U盘或光盘等外设插入设备后，会自动挂载在这个目录下。有些系统会没有这个目录，则会挂载在 `/mnt` 下。

## 1.8. ／mnt
挂载点，所有的设备都会在这里显示出来。

## 1.9. /opt
通常用来安装大型软件。

## 1.10. /proc
Processes，系统进程的实时信息，并不存在于硬盘上，是存在于内存中的一个虚拟目录。    
可以在此目录下用 `cat` 命令查看系统相关信息。

## 1.11. /root
root 用户的 `home` 目录。默认权限 `700` 。

## 1.12. /sbin
Super binary，一般用于存放分区、格式化等超级用户权限才能做的命令。默认权限 `744` 。

## 1.13. /selinux
Security Linux，存放有关安全方面的文件。

## 1.14. /srv
Service，存放系统服务相关文件。

## 1.15. /sys
System，和 `proc` 一样，也是创建在内存中的伪文件系统。用于存放硬件设备的驱动程序信息。

## 1.16. /tmp
Templates，临时目录，解压缩文件、安装文件等等。每个一段时间会自动清空文件夹。

## 1.17. /usr
Unix Shared Resources，一般的软件默认安装在这里。例如：ruby、python、wget、mysql ……

## 1.18. /var
Variable，变量信息。类似log、mail、www之类的经常会发生变化的信息通常保存在这里。


