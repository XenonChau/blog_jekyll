---
layout: post
title: "[转]Windows NT网络维护技巧12则"
description: "Windows, NT, 网络维护"
modified: 2006-10-02
category: Windows
tags: [运维]
comments: false
share: false
---

> 原文地址：[Windows NT网络维护技巧12则](http://tech.sina.com.cn/soft/2000-08-14/623.html)

在现在的网络应用中，以 Windows NT 作为服务器的操作系统的用户越来越多。Windows NT 4.0 作为一个高性能32位多任务、多用户的网络操作系统，由于其界面的友好性和强大而直观的管理功能，无论对网络新手还是资深系统管理员，都可以迅速地构造起一套基于 Windows NT 的网络环境，从而赢得了众多用户的青睐。但一些 NT 用户却在使用 NT 时遇到一些意想不到的麻烦，从而使其 NT 系统不能高效率运行。下面笔者结合自己在实际工作的使用体会，将一些有关 Windows NT 的使用技巧介绍给大家，希望能给大家在使用 Windows NT 的过程中有所启发。

## 1、自动登陆网络

通常情况下，用户在进入 WINNT 网络之前必须输入自己的用户名称以及口令。但是 WINNT 也提供了启动时自动以某一个组的用户名称和口令进行访问 WINNT ，而不需要通过人工设置的方法来输入登陆网络的参数。要实现自动登陆功能，必须要进行如下配置：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS NT\CURRENT VERSION\WINLOGON]
```

B、用鼠标单击右边的 `AUTOADMINLOGON` 键值名称，编辑器就会弹出一个名为 ***字符串编辑器*** 的对话框，在该对话框的文本栏中输入数值 `1`。

C、接着再用鼠标选择右边的 `DEFAULTDOMAINNAME` 键值名称，并在随后弹出的文本栏中输入所要登陆的域名名称或所要访问的计算机名称，例如输入 `DOMAIN` 域或 `COM` 计算机名称，然后单击“确定”按钮。

D、按照同样的操作方法，选择右面的 `DEFAULTUSERNAME` 键值名称，并在“字符串”文本框中输入登陆网络的用户名称，例如输入管理员名称 `ADMINISTRATOR`，并单击“确定”按钮。

E、最后在注册表编辑器中，用鼠标单击“编辑”菜单并在下拉菜单中选择“新建键值”命令，然后在注册表右边的列表中，输入键值名称为 `DEFAULTPASSWORD`，键值类型为 `REG_SZ`，接着单击“确定”按钮。

F、 用鼠标双击 `DEFAULTPASSWORD` 键值，在弹出的对话框中输入用户的密码，在这里我们输入系统管理员的口令作为登陆网络的密码，输入完成后单击“确定”按钮结束设置工作。

G、 让计算机重新启动，缺省设置的用户将会自动登陆到指定的网络中去。
如果用户日后不再需要自动登陆功能时，只需要把 `AUTOADMINLOGON` 的键值改为数值 `0` 即可。

## 2、取消系统检测串口，提高NT系统启动速度

计算机每次启动都会对计算机的硬件要重新检测一遍，这需要花费一定的时间，也因此就减慢了计算机的启动速度。在这里笔者向大家介绍一下通过一些设置来取消系统检测串口，从而达到提高 NT 系统启动速度的目的，具体步骤如下：

A、在开始菜单中，用查找的方法将 `Boot.INI` 文件找出来，然后将该文件的“只读”属性屏蔽掉，以便于我们在其中进行一些相关改动。

B、接着用一个文本编辑器将 `Boot.INI` 文件打开，并修改 `[operating system]` 段的内容，将其中每一行后加上 `NoserialMice` 参数，如下所示：

修改 `Boot.INI` 文件，

```
[operating system]
multi(0) disk(0) rdisk(0)
partition(1)\WINNT40="Windows NT
Workstation Version4.0"/NoSerialMice
```

C、把上述修改的内容保存起来，文件名仍为 `Boot.INI`。

D、退出 Windows NT ，重新启动计算机后上述配置就会生效。

## 3、提升 NTFS 性能

如果用户想提升 NTFS 的性能，也可以通过修改注册表的方法来达到目的，具体实现步骤如下：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem]
```

B、在注册表编辑器中用鼠标单击“编辑”菜单，并在下拉菜单中选择“新建”菜单项，并在其弹出的子菜单中单击 `DWORD` 值。

C、 在编辑器右边的列表中输入 `DWORD` 值的名称为 `NtfsDisableLastAccessUpdate`。

D、 接着用鼠标双击 `NtfsDisableLastAccessUpdate` 键值，编辑器就会弹出一个名为 ***字符串编辑器*** 的对话框，在该对话框的文本栏中输入数值 `1`，其中 `0` 代表“取消”该项功能，`1` 代表“启用”该项功能。

E、设置好后，重新启动计算机就会使上述功能有效。

## 4、修复镜像组

A、当镜像磁盘组中的驱动器发生故障时，系统自动向其余的驱动器发出发送数据请求，留下工作驱动器单独运行。此时，用户需要进入 `Disk Administrator`，选择镜像组，再选择 `FaultTolerance/Break Mirror`，将镜像组分为两个独立部分。

B、工作的驱动器得到磁盘组所用的驱动器盘符，故障驱动器得到系统的下一个有效盘符。关闭 NT Server，更换一个相同型号的硬盘驱动器。

C、重新启动 NT Server，运行 `Disk Administor`，在新驱动器上选择分区和未用空间，选择 `Fault Tolerance/Establish Mirror` 即可对新驱动器作镜像。

## 5、禁止光盘的自动运行功能

大家都很清楚每当光盘放到计算机中时，WINNT 就会执行自动运行功能，光盘中的应用程序就会被自动运行，而我们在实际工作中有时不需要这项功能，那么如何屏蔽该功能呢。此时，我们同样可以修改注册表使此功能失效，具体做法如下：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Cdrom]
```
B、在编辑器右边的列表中用鼠标选择 `AUTORUN` 键值。
C、接着用鼠标双击 `AUTORUN` 键值，编辑器就会弹出一个名为 ***字符串编辑器*** 的对话框，在该对话框的文本栏中输入数值 `0`，其中 `0` 代表“禁用”光盘的自动运行功能，`1` 代表“启用”光盘的自动运行功能。
D、设置好后，重新启动计算机就会使上述功能有效。

## 6、为一些非SCSI接口CD-ROM进行安装配置

当我们在 Windows NT 3.5 操作系统中安装新的 CD-ROM 时，由于 Windows NT 3.5 不能自动识别非 SCSI 接口 CD-ROM，所以必须通过手工安装的方式进行安装。

A、将相应的非 SCSI 接口的 CD-ROM 驱动程序从安装盘拷贝到 `WINNT\SYSTEM32\DRIVERS` 目录下。

B、在 Windows NT 主群组中运行 Setup 程序。

C、从 Option 菜单中选择 `Add/Remove SCSI Adapters`。

D、选择 `Add` 按钮，为你的非 SCSI 接口 CD-ROM 选择相应的驱动程序。

E、按 `INSTALL` 按钮进行配置。

F、重新启动计算机后就可以使用 SCSI 接口的 CD-ROM 了。

##7、增加“关闭系统”按钮

在 NT 计算机中，“关闭系统”按钮作为缺省值在登陆对话框中提供，这个任务按钮允许用户不必先登陆即可关闭系统。在 NT　SERVER 中虽然没有这个功能，但可以通过修改注册表，使系统在登陆对话框中增加一个“关闭系统”的按钮，具体操作方法如下：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS NT\CURRENTVERSION\WINLOGON]
```

B、在编辑器右边的列表中用鼠标选择 `SHUTDOWNWITHOUTLOGON` 键值。

C、接着用鼠标双击 `SHUTDOWNWITHOUTLOGON `键值，编辑器就会弹出一个名为“字符串编辑器”的对话框，在该对话框的文本栏中输入数值 `1` ，其中 `0` 代表取消“关闭系统”按钮，`1` 代表增加“关闭系统”按钮。

D、退出后重新登录，在登录的界面中会增加一个“关闭系统”的按钮。

## 8、在NT下创建一个镜像集

A、先用 `Disk Administerator` 创建镜像集的第一个分区表，选中该分区，在另一个磁盘驱动器内的磁盘空间的未用区域上进行 Ctrl+鼠标单击 操作，以把未用的磁盘区域和第一个分区均选上。

B、从 `Disk Ad ministor` 的 `Fault Tolerlance`(容错)菜单中选中 `Establish Mirror` (建立镜像 )，`Disk Administor` 将在被选自由盘区外创建一个磁盘分区。该分区与原有分区一样大，并包含原分区表上所有数据的备份。

C、如果要取消镜像集(并非删除镜像集)，即仅仅停止两个分区之间的数据复制，只需从 `Disk Administor` 中选择 `Fault Tolerance/Break Mirror`。

## 9、登录局域网超时自动断开

在登录Windows NT网络时，有可能用户不小心输错了登录参数或其他原因，导致了登录网络可能需要花费好长时间，这种情况是我们不想看到的。为了解决这种问题，我们可以通过注册表，来配置为闲置超时断开，以分钟为单位，具体步骤为：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters]
```

B、在编辑器右边的列表中用鼠标选择 `AUTODISCONNECT `键值。

C、接着用鼠标双击 `AUTODISCONNECT` 键值，编辑器就会弹出一个名为 **字符串编辑器** 的对话框，在该对话框的文本栏中输入数值 `1`，其中 `0` 代表取消自动断开功能，`1` 代表使用自动断开功能。

D、退出后重新登录网络，上述功能就会生效。

## 10、改变远程访问服务的缺省端口传输速度

Windows NT 远程访问服务为每个 RAS 串行端口设置两种 BPS 速度：载波 BPS 与联接 BPS 速度，前者是指两个 Modem 通过电话线传输数据的速度，后者则指 Modem 与主机串口间的数据传输速度；当远程服务被启动时，计算机首先将其联接 BPS 速度值存放于 `\System Root\\System32\RAS\Modem.INF` 文件中，以 `MAXC ARRIERBPS` 参数形式存放，然后将该值保存在 `RASSERIAL.INF` 文件中，以后每次进行传输时对串口作初始化，只需改变 `SERIAL.INI` 文件，即可改变串口传输速度，具体操作方法如下：

A、将 `SERIAL.INI` 文件用编辑器打开。

B、将 `INITIALBPS` 参数改为自己所希望的数值。

C、将上述改动保存成一个文件，文件名仍为 `SERIAL.INI`。

D、打开“开始”菜单，并单击“运行”命令，在运行栏中输入 `RasAdmin` 命令。

E、从服务器菜单中选择 `Stop Remote Access Service`，再选择 `Start Remote Access Service`，上述设置就会生效。

## 11、自动检测慢网登陆

上面我们曾经说过，在登录 NT 网络时有可能很慢。同样地，如果 Windows NT 检测速度有困难，可以取消。具体实现步骤为：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon]
```

B、在编辑器右边的列表中用鼠标选择 `SlowLinkDetectEnabled` 键值，如果没有该键值，必须新建一个 `DWORD` 值，名称设置为 `SlowLinkDetectEnabled` 。

C、接着用鼠标双击 `SlowLinkDetectEnabled` 键值，编辑器就会弹出一个名为 ***字符串编辑器*** 的对话框，在该对话框的文本栏中输入数值 `1`，其中 `0` 代表取消自动检测慢网登陆功能，`1` 代表使用自动检测慢网登陆功能。

D、退出后重新登录网络，上述设置就会起作用。

## 12、禁止拨入访问

如果用户的计算机上面有重要的信息，有可能不允许其他人随便访问。那么如何禁止其他人拨入访问你的计算机，减少安全隐患呢，具体步骤为：

A、打开注册表编辑器，并在编辑器中依次展开以下键值：

```
[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\Network]
```

B、在编辑器右边的列表中用鼠标选择 `NoDialIn` 键值，如果没有该键值，必须新建一个 `DWORD` 值，名称设置为 `NoDialIn`。

C、接着用鼠标双击 `NoDialIn` 键值，编辑器就会弹出一个名为 ***字符串编辑器*** 的对话框，在该对话框的文本栏中输入数值 `1`，其中 `0` 代表禁止拨入访问功能，`1` 代表允许拨入访问功能。

D、退出后重新登录网络，上述设置就会起作用。


