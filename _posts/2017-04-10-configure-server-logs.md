---
layout: single
title:  "服务器配置日志(Configure Server Logs)"
date:   2017-04-10 20:56:38 +0800
categories: Linux
---

## 第一日(Day 1)-2016.11.11

> 今天双十一，阿里云 ECS 五折销售，犹豫了很久还是剁了一台最低配的。

首先选择了自己比较熟悉的 Ubuntu 14.04 LTS 。

第一天就在 root 用户下，安装或配置了 git 、ssh 、 apache 、 python3 、 PHP 、 mysql-server 、 mysql-client 等等一系列软件，成功了一部分，失败了一部分。直到安装 ruby on rails 的时候，才发现失败的原因是**不能在 root 用户下使用 gem install**。

因为好多东西我只能配置但是不会修改。思索了一会：*决定重装系统！* 就这样，第一天就这样愉快的过去了。

## 第二日(Day 2)-2016.11.12

> 一觉睡到下午，起来吃了点饭，又继续折腾服务器。

这次行动就没有那么鲁莽了，先对比了一下 Debian 和 RHEL 的区别，又知乎了一下就业形势：*决定选择 CentOS ！*

登陆服务器之后第一件事就是 `useradd -g root admin` ，给自己创造了一个属组为 `root` 的管理员账号(username 略，以下用 admin 简称)。因为昨天走过一次流程了，顺便也加了 `git:git` 用户。
**别忘了设置密码：`passwd admin`**
**还要把 `admin` 加入 `/etc/sudoers`**(`chmod 770 /etc/sudoers`)
**开启免密码登陆需要打开 `/etc/ssh/sshd_config` 中 `PubkeyAuthentication yes`**(`chmod 770 /etc/ssh/sshd_config`)

然后 `su admin` 切换到管理员账户进行环境配置。

### OpenSSH

*以下命令行中本地主机和远程主机分别用 user@local 和 admin@remote 代替。*

本地需要先生成 ssh key，如果之前已经生成过了略过这一步。

`ssh-keygen -t rsa -C "user@local"`

需要注意的是 `"user@local"` 并不是你的电子邮件，这个是一个 identifier ，在一些 authorized_keys 列表中用来区分不同主机的。写一个自己能记住的，主机也能认识的名字，格式最好是 `用户@主机` 。

之后出现的目录提示如果不修改，则会在提示中的默认路径 `~/.ssh/` 内生成公钥 `id_rsa.pub` 和私钥 `id_rsa` 。密码可输入亦可忽略。

然后查看一下自己的公钥：

`cat ~/.ssh/id_rsa.pub`

会输出类似这样的文字：（我把我的公钥修改过粘贴过来的）

```
ssh-rsa AADFB3NzaC1yc21234567890/j3xzZFP6KkPRbvrK/n5Eo3IaOEfOAssvjK1ye/TBi8EXGoOl+BUWBhpt1X1M4iDkYY23q12345678901234567890YVMitWldnwekgdkxtIIKPoaKUsmMlEDxM741FD91234567890-1234567890vuHNN9VvQdtY6VDWkZLhNoePbiomFFdiBz89PLX6ssWCOTOS+JgUX0VSuVu003iV123456789012345678901234567890RjwqVBAJfZ2UpK9UThPTzdXB2d/+mUbqxc8oZdyqXVoNbCsumbz3wfadxLpwPU6RlGnxHQfrZH9M21kZ47GL user@local
```

远程服务器上在每个用户的 `~/.ssh/authorized_keys` 里添加这段公钥。如果有其他用户需要登陆，每行一个。

**划重点！（敲黑板）**

为了能让用户免密码登陆，修改如下目录权限：

`/home/$USER/` : 755（drwxr-xr-x）(744,740都可以)

`/home/$USER/.ssh` : 700（drwx------）

`/home/$UESR/.ssh/authorized_keys` : 600 （-rw-------）

修改`/etc/ssh/sshd_config`中：
```
RSAAuthentication yes
PubkeyAuthentication yes
```

**其实：用`ssh-copy-id username@remotehost`就行了。300多行的脚本帮我们做了这些事情。**

**如果服务器系统重装了，请务必删掉 local `known_hosts` 中保存的主机信息。**

## 第三日(Day 3)-2016.11.13

### Git

配置 git 仓库服务器不要去搜索引擎搜索，直接看廖雪峰老师的教程就行——你的 git 用户数量根本用不到 gitosis 来管理 ssh 证书！

**要注意目录权限！要注意目录权限！要注意目录权限！**

然后按照 SSH 设置方式，把 `/home/git/.ssh/authorized_keys` 及目录权限都配置好，就可以免密码使用 git 了。

**user@local:**

clone 下来的仓库第一次是不能直接 push 的，按照贴心的提示加上参数就可以了。

**admin@remote:**

需要注意的是，git 服务器上保存的 push 文件，存在 objects 目录下，用一种奇怪的打包方式保存着。可以通过 `cat /home/git/<respository_name>.git/objects/<short_folder_name><file_name>` 来查看保存的是什么文件。

如果想用类似 GitHub 那样可视化的检查文件，可以通过 `<respository_name>.git/hooks/` 来自动部署到 Apache 服务器上。

不过还是推荐使用 GitBucket private repository，不把鸡蛋装到同一个篮子里。


## 第四日(Day 4)-2016.11.14

### Apache

直接使用 `# yum install apache2` 即可

不推荐直接使用。用NginX反向代理来配置apache最好。

### NginX

> 如果编译安装，参考官方[《Nginx中文文档》](http://www.nginx.cn/doc/)

这里我选择了一个简单的方式：

`yum install nginx`

配置：

> 这里需要另开一篇文章单独介绍。

## 第五日(Day 5)-2016.11.15

### MySQL

`yum install mysql`

通常安装一个包，最好是去官网下载一个最新版的。
wget 下载到 /usr/local/src/ 

```
./configure
make
make install
```

cmake的参数参考官网：
[Chapter 4 MySQL Source-Configuration Options](https://dev.mysql.com/doc/mysql-sourcebuild-excerpt/5.5/en/source-configuration-options.html)

```
这些都是典型的使用GNU的AUTOCONF和AUTOMAKE产生的程序的安装步骤。
./configure是用来检测你的安装平台的目标特征的。比如它会检测你是不是有CC或GCC，并不是需要CC或GCC，它是个shell脚本。
make是用来编译的，它从Makefile中读取指令，然后编译。
make install是用来安装的，它也从Makefile中读取指令，安装到指定的位置。
AUTOMAKE和AUTOCONF是非常有用的用来发布C程序的东西。
-----
1、configure，这一步一般用来生成 Makefile，为下一步的编译做准备，你可以通过在 configure 后加上参数来对安装进行控制，比如代码:./configure –prefix=/usr 意思是将该软件安装在 /usr 下面，执行文件就会安装在 /usr/bin （而不是默认的 /usr/local/bin),资源文件就会安装在 /usr/share（而不是默认的/usr/local/share）。同时一些软件的配置文件你可以通过指定 –sys-config= 参数进行设定。有一些软件还可以加上 –with、–enable、–without、–disable 等等参数对编译加以控制，你可以通过允许 ./configure –help 察看详细的说明帮助。
2、make，这一步就是编译，大多数的源代码包都经过这一步进行编译（当然有些perl或python编写的软件需要调用perl或python来进行编译）。如果 在 make 过程中出现 error ，你就要记下错误代码（注意不仅仅是最后一行），然后你可以向开发者提交 bugreport（一般在 INSTALL 里有提交地址），或者你的系统少了一些依赖库等，这些需要自己仔细研究错误代码。
3、make insatll，这条命令来进行安装（当然有些软件需要先运行 make check 或 make test 来进行一些测试），这一步一般需要你有 root 权限（因为要向系统写入文件）。
-----
Linux的用户可能知道，在Linux下安装一个应用程序时，一般先运行脚本configure，然后用make来编译源程序，在运行make install，最后运行make clean删除一些临时文件。使用上述三个自动工具，就可以生成configure脚本。运行configure脚本，就可以生成Makefile文件，然后就可以运行make、make install和make clean。
configure是一个shell脚本，它可以自动设定源程序以符合各种不同平台上Unix系统的特性，并且根据系统叁数及环境产生合适的Makefile文件或是C的头文件(header file)，让源程序可以很方便地在这些不同的平台上被编译连接。
这时，就可运行configure脚本了，运行configure脚本，就可产生出符合GNU规范的Makefile文件了：
$ ./configure
到此时，就可以运行make进行编译，在运行make install进行安装了，最后运行make clean删除临时文件。
$ make
$ make install           (注：运行这个要有足够的权限)
$ make clean
利用configure所产生的Makefile文件有几个预设的目标可供使用，其中几个重要的简述如下：
make all：产生我们设定的目标，即此范例中的可执行文件。只打make也可以，此时会开始编译原始码，然后连结，并且产生可执行文件。
make clean：清除编译产生的可执行文件及目标文件(object file，*.o)。
make distclean：除了清除可执行文件和目标文件外，把configure所产生的Makefile也清除掉。
make install：将程序安装至系统中。如果原始码编译无误，且执行结果正确，便可以把程序安装至系统预设的可执行文件存放路径。如果用bin_PROGRAMS宏的话，程序会被安装至/usr/local/bin这个目录。
make dist：将程序和相关的档案包装成一个压缩文件以供发布。执行完在目录下会产生一个以PACKAGE-VERSION.tar.gz为名称的文件。 PACKAGE和VERSION这两个变数是根据configure.in文件中AM_INIT_AUTOMAKE(PACKAGE，VERSION)的定义。在此范例中会产生test-1.0.tar.gz的档案。
make distcheck：和make dist类似，但是加入检查包装后的压缩文件是否正常。这个目标除了把程序和相关文件包装成tar.gz文件外，还会自动把这个压缩文件解开，执行 configure，并且进行make all 的动作，确认编译无误后，会显示这个tar.gz文件可供发布了。这个检查非常有用，检查过关的包，基本上可以给任何一个具备GNU开发环境-的人去重新编译。
```

**注意：1G内存无法编译安装MySQL5.5，请更换低版本或者临时增加swap区。**


**在linux下增加临时swap空间**

> step 1:

```shell
sudo dd if=/dev/zero of=/home/swap bs=64M count=16
# of=/home/swap,放置 swap 的空间; 
# count=16 的大小就是增加的 swap 空间的大小，bs = 64M 就是块大小
# 所以总共空间就是 bs * count = 1024 MB. 
# 这里分配空间的时候需要一点时间，等待执行完毕。
```

> step 2:

```shell
sudo mkswap /home/swap
# 可能会提示warning: don't erase bootbits sectorson whole disk. Use -f to force，不用理会。
# 把刚才空间格式化成 swap 格式
```

> step 3:

```shell
sudo swapon /home/swap
# 激活刚才创建的 swap 空间
```

> step 4:

```
执行你相关的操作，如make
如果创建了临时空间仍然提示 "g++: 内部错误：Killed (程序 cc1plus)"，可能分配的空间不够大，可继续分配更大的空间。
```

**关闭:**

> step 1:

```shell
sudo swapoff /home/swap
```
> step 2:

```shell
sudo rm /home/swap
```

```
[Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
[Warning] InnoDB: New log files created, LSN=45790
[Warning] InnoDB: Creating foreign key constraint system tables.
[Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 128f7e01-1ae2-11e7-8c10-00163e059525.
[Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
[Note] A temporary password is generated for root@localhost: GiB%3ca:,qw(
```


配置好之后可以用以下命令登录到 mysql cli：
```
mysql -uroot -p
# 这个地方输入刚才安装成功之后给你生成的临时密码。
# 比如我的初始密码：GiB%3ca:,qw(
```

第一次登录会提示你要修改密码，我是 5.7.17 它总提示我用 ALTER USER 修改。
搜索文档结果发现这是 5.7.5 之后的命令。具体教程参考官网：
[B.5.3.2 How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)

MySQL 5.7.6 and later:
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
```
MySQL 5.7.5 and earlier:
```
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('MyNewPass');
```

如果你之前配置目录写错了，请同时修改
`/etc/my.cnf` 和 `path/to/mysql/bin/mysqld`

最重要的是要配置一个高可用的mysql，需要修改/etc/my.cnf

```
# 这个需要单开一篇文章来详述配置的每条命令。
```

## 第七日(Day 7)-2016.11.17

> 因为我买的是最低配置的 ECS，导致编译安装 MySQl 用了超过24小时。
> 昨天休息一天，今天继续。

### PostgreSQL

遵循 BSD 协议的开源数据库。（暂时还没学会！）

### Ruby on Rails
#### Gem

看名字就知道，gem是宝石的统称，ruby是红宝石，所以gem一定跟ruby有关系。

安装：
`# yum install rubygems`

修改 gem 源为国内镜像：
`$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems/org/`

然后检查一下是否是唯一源：
`$ gem sources -l`

**local 创建 rails 工程时，`rails new project` 在 `bundle install` 卡住的解决办法：**

```
find /path/to/your/gems/ -name "Gemfile" | xargs sed -i -e "s%rubygems.org%gems.ruby-china.org%g"

# \ / : % 都可以作为定界符来使用。。。
```

批量修改全部的Gemfile源。

我这里是 macOS，包括开发 iOS 时用到的 cocoaPods 也悉数在目。

#### RVM

用于安装不同版本的ruby，以及轻松切换ruby环境。

先验证 `gpg2` 需要下载如下文件：
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

安装 RVM：

```
\curl -sSL https://get.rvm.io | bash # 最新的开发版
# 或者
\curl -sSL https://get.rvm.io | bash -s stable # 最新的发行版（推荐）
```

然后需要将 RVM 添加到命令行：

```
source ~/.zshrc # 使用 zsh
# 或者
source ~/.profile # 使用 bash
```


#### Ruby

推荐使用 RVM 来安装 Ruby：

查看所有版本：`$ rvm known list`

安装某个版本：`$ rvm install 2.4.0`

切换到某个版本：`$ rvm use 2.4.0 --default`
> 此时如果遇到了 `rvm is not a command`
> 按照提示重新登录 shell `/usr/bin/zsh --login`

### Python3

**貌似只有编译安装方式，同MySQL。**

使用
`ln -s /usr/local/python3.6/bin/python3 /usr/bin/python3`
创建一个软连接到系统环境变量中。

我在服务器上将 `python` 重新链接到 `python3` 上，并修改原 `python` 为 `python2`。
这样会导致一个问题：很多安装脚本是用py2书写的，和py3在格式上会有差异。有时候 `./configure` 提示 `syntex error` 的时候不要惊慌，按照提示打开指定文件，修改首行 `#!/usr/bin/python` 为 `#/usr/bin/python2` 即可。

#### Flask
#### Tornado
#### Django
这几个略过不讲了，安装没什么难度。

### PHP7

阿里云源包含了绝大部分的包，不用下面那么麻烦。

`yum install epel-release`安装EPEL源（Extra Packages for Enterprise Linux）
`rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm` 获取PHP7源
`yum install  –enablerepo=remi-php70  -y php-opcache php-devel php-mbstring php-mcrypt php-mysqlnd` 指定源来安装PHP7。你可以使用 `yum info –enablerepo=remi-php70 php` 查看版本。
或者直接执行`yum install php71`

**不过朋友推荐用编译安装方式，如果想增加插件，可以重新编译。**

具体请参考简书文章：
[《Centos7 安装 PHP7最新版》--我来自塘湖(2016.03.24 15:25)](http://www.jianshu.com/p/246ffcd5e77d)

#### ThinkPHP
#### Laravel
#### Yii 2

这三个也略过了。

### Jekyll

这个不想在自己服务器上部署了，用gh-pages服务挺好的。

### Markdown

一个解释器而已，不想部署了。

## 结语（Conclusion）

本篇文章重点在于：

* Linux 的权限分配——包括用哪些用户来运行哪些服务。秉承着最小权限原则（Least Priviledge）来设置每个目录的权限，分配每个服务的运行者。
* 简述了 ssh 远程登录的操作和需要注意的问题。
* 熟悉了一些编译源码来安装软件包的方法，引申出来的问题学会了如何创建临时 swap 区。
* 想要统一修改 Gemfile 的源，学习到了 find exec 后面跟着的 xargs 参数 以及 sed -i -e 的用法。
* 学会了如何编译安装 PHP7 以及编译时所加参数的意义，学会了开启 fpm 跟 NginX 联动，学会了如何编译安装新插件。
* 学习到了如何在一台服务器上使用 NginX 开启多个不同的 web server，并且绑定不同的域名。

**学习使我快乐！**

> 全篇写完并修改于：2017-04-10 20:52



