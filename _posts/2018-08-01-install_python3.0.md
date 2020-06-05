---
layout: post
title: CentOS7编译安装Python3.7.0
categories: Blog
description: Python环境安装。
keywords: Python，CentOS7
---

### 说明：
CentOS7系统自带了Python2环境，如果对于Python3环境有需求，那么就不得不自己安装了。本文提供了在CentOS7系统下手动编译安装Python3.7.0的详细步骤，其它各版本Python的安装步骤类似。

本文步骤中出现的命令，除了username需自己修改为实际用户名，其它均可完全复制。

### 环境：
虚拟机下的CentOS7，Python-3.7.0.tar.xz

### 思路：
在Windows下通过安装软件，过程几乎全部都是双击运行，安装向导中个性化设置，然后完成，设置可以一路下一步。在Linux下通过源码包编译安装也是类似的过程，只不过在Linux下的各种配置都是通过命令实现的。
拿编译安装Python为例：

1，首先下载源码包，就像是Windows下的下载安装包。

2，其次，个性化设置，就像是Windows下选择安装目录，是否添加到环境变量等过程，不过Linux下是在命令行中配置。

3， 编译安装，其实就是安装的过程了。

### 步骤：
1，有一个su权限的非root用户，或者root账户，为了方便本文直接使用root账户进行安装。

2，安装依赖包。原因为CentOS7小版本众多不同用户安装系统时选择安装的具体内容也不相同，比如有的安装了开发工具（Development Tools），有的则没有，且Python3的运行依赖一些其它模块。此外Python3.7较之前的版本如Python3.6.X需要一个新的依赖libffi-devel，否则后面make会出错。
```
# yum update
# yum groupinstall "Development Tools"
# yum -y install libffi-devel zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel zlib*
```

3，下载Python源码。下载方式有多种，直接浏览器下载，或下载软件下载，或命令行下载。比如我们把下载的源码包放在/home/username/Downloads文件夹下。
```
# cd /home/username/Downloads
# wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
```

4，目录不变，解压文件。解压出文件夹Python-3.7.0。
```
# tar -xf Python-3.7.0.tar.xz
```

5，切换工作目录到/usr/local，新建python3文件夹，并将解压出来的Python-3.7.0文件夹移动到/usr/local/python3目录下，此步骤的目的仅仅因为个人喜欢文件存放比较规整，让各个文件放在该放的文件夹，方便查找。
```
# cd /usr/local
# mkdir python3
# mv /home/username/Downloads/Python-3.7.0 python3
```

6，配置编译和安装。
```
# cd python3/Python-3.7.0
# ./configure --prefix=/usr/local/python3 --enable-optimizations
# make
# make install
```

7，不出意外的话，等待一段时间，安装就完成了。此时系统存在两个版本的Python，一个为自带的Python2，一个为我们刚装好的Python3，在使用的时候，我们可能有两种需求，一种为使用python命令的时候都是用的我们安装的Python3，另一种为使用python命令时用的是系统的Python2，使用python3命令的时候才是用的我们安装的Python3，所以可以根据需求选择自己的设置方式。

8，方式一：python命令为新安装的Python3，这种方式的效果其实类似于Windows系统中把快捷方式指向我们自己制定的应用，但这样造成的后果就是系统原本在yum中所需要的Python2变成了我们安装的Python3版本，所以我们还需要修改相关的文件，将他们所需的Python版本指回到系统所带的Python2版本。

下面是设置python命令：
```
# cd /usr/bin
# mv python python_old
# ln -s /usr/local/python3/bin/python3.7 /usr/bin/python
# ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip
```

下面是修改yum相关文件的步骤：
```
# cd /usr/bin
# ls -ol yum*
```
可以看到包括gnome-tweak-tool和urlgrabber在内共8个文件：
依次用vim打开每个文件
```
# vim yum
```

将文件头部 #!/usr/bin/python改为#!/usr/bin/python2，即加一个2，修改所有上面8个文件，保存关闭即可。对于vim的操作，i 插入模式，ESC退出，：末行模式，wq，保存退出。更详细的使用方法可自行找文档学习。

9，方式二：步骤很简单，只是加两个3就可以了。
```
# cd /usr/bin
# ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3
# ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3
```

10，如果需要更换python的pip下载源，可以通过命令行临时指定，也可以设置配置文件永久指定，源地址可以自行指定国内的镜像源。

临时设置：
```
# pip install packagename -i https://pypi.douban.com/simple
```
添加配置文件：
```
# cd ~
# mkdir .pip
# cd .pip
# vim pip.conf
```
配置文件内容如下：
```
[global]
index-url=https://mirrors.aliyun.com/pypi/simple/
trusted-host = mirrors.aliyun.com
```
11，大功告成，后续可安装Pycharm，Atom等IDE。
