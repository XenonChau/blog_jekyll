---
layout: post
title: "[转]Windows2000+IIS+PHP5配置全能空间的方法"
modified: 2007-11-28
categories: Windows
tags: [运维]
comments: false
share: false
---

喜欢php的人越来越多，而且大多数朋友都习惯在windows环境下开发和调试程序，但是如何在windows系统下配置php让其同时支持asp和php则一直困扰着初学者。本文将提供windows 2000（或者windows XP、windows 2003）+IIS+PHP5配置全能空间（同时支持asp和php）的方法。 

一般在已经安装iis的windows系统上已经默认支持了asp，那么下面主要讲解如何让其也支持php并且支持MYSQL和gd库。

Windows 2000下PHP+IIS的安装方法

1、把php-5.0.0（目前有5.0.5最新版本，推荐使用）-Win32.zip解压放到C:\php。

2、将php.ini-dist复制到Winnt目录下并改名为php.ini，复制php5ts.dll和libmysql.dll到WINNTsystem32中。

3、接下来在IIS中进行设置，打开默认站点属性对话框，切换到“主目录”选项卡，然后点“配置”按钮打开“应用程序配置对话框”，点“添加”按钮打开“添加/编辑应用程序扩展名映射”对话框。在“可执行文件”输入框中，指向c:phpphp5isapi.dll，在“扩展名”输入框里面输入“php”（不包括引号），并选中“脚本引擎”和“检查文件是否存在”两个复选框，一路确定并退出IIS。

[](../images/win2k_iis_php5/iis_manager.gif)

[](../images/win2k_iis_php5/app_mapping.gif)


至此服务器应该已经能够同时支持asp和php了。

下面对php.ini进行配置让php支持MYSQL和GD库

1、在记事本中查找“extension_dir”，然后把
extension_dir = "./"
修改为
extension_dir = "C:\php\ext"

2、在记事本中查找“;extension=php_mysql.dll”和“;extension=php_gd2.dll”，然后把前面的“;”去掉，即把
;extension=php_mysql.dll
改为
extension=php_mysql.dll

把
;extension=php_gd2.dll
改为
extension=php_gd2.dll

3、保存好php.ini，然后重起IIS即可


