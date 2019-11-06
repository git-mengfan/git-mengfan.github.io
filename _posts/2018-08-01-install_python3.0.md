---
layout: post
title: CentOS7编译安装Python3.7.0
categories: Blog
description: Python环境安装。
keywords: Python，CentOS7
---

### 说明：
对于新手，就像我自己一样，在刚接触Linux系统的时候，对于安装配置各种环境总是比较困惑，因为命令较多，步骤也多，漏掉一步有时会出现各种错误，所以参考别人的文档，总结了一下配置Python3.7的详细步骤。对于Python的其它版本，方法是一样的。步骤中出现的命令，除了username为自己修改实际名字外，其它均可完全复制，具体的参数，比如解压时-x的含义则不过多解释，可以自己help一下。

### 环境：
虚拟机下的CentOS7，Python-3.7.0.tar.xz

### 步骤：
1，可选则安装Python之前更新yum，当然需要管理员权限，注意即可。简单起见，整个安装过程可以都采用管理员运行，在安装CentOS7的时候可能不同需求安装的初始包也不相同，比如有的安装了开发工具（Development Tools），有的没有安装，没有安装的可能需要手动安装一下。因为我们要安装的是3.7，3.7较之前的版本需要一个新的依赖包libffi-devel，需要安装一下，否则后面make会出错。
```
# yum update
# yum groupinstall "Development Tools"
# yum -y install libffi-devel zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel zlib*
```

2，选择或不选择第1步，接下来就是我们开始安装Python3了。我对于文件的放置有一种偏执，比如有的文件显示乱码就想把它改名字或者删掉或者查找怎么回事，反正要修正过来，所以我会建议把文件放在该放的地方。

3，下载Python源码。下载方式有多种，直接浏览器，或下载软件，或命令行。比如我们把下载的源码包放在/home/username/Downloads文件夹下。
```
# cd /home/username/Downloads
# wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
```

4，文件目录不变，解压文件。解压出文件夹Python-3.7.0。
```
# tar -xf Python-3.7.0.tar.xz
```

5，切换工作目录到/usr/local，新建python3文件夹，并将解压出来的Python-3.7.0文件夹移动到/usr/local/python3目录下。
```
# cd /usr/local
# mkdir python3
# mv /home/username/Downloads/Python-3.7.0 python3
```

6，编译安装。
```
# cd python3/Python-3.7.0
# ./configure --prefix=/usr/local/python3 --enable-optimizations
# make
# make install
```

7，不出意外的话，需要等待运行一段时间，安装就完成了。但整个环境配置还没结束。系统默认采用的Python2，运行命令
```
# python --version
```

可以查到系统安装的python2的版本信息，但这不是我们想要的，因为我们需要用的是python3，我们期望的是在命令行运行python命令时是使用的python3即我们安装的版本，所以我们需要修改一下系统的调用，正式的说法也就是软链接。前两条命令可以理解为将python2的调用重新命名以使得python这个命令成为调用python3的命令。如果想保存两个版本，那么就不需要下面代码的第二行命令，将三四行最后加上3，即 /usr/bin/python3，如果这样修改，那么就不需要第8步。
```
# cd /usr/bin
# mv python python_old
# ln -s /usr/local/python3/bin/python3.7 /usr/bin/python
# ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip
```
8，到此，还需要再次修改一些文件，因为系统依赖的是python2，yum包管理工具也是使用的python2版本，但我们把系统调用改为了3，所以需要修改一些文件使yum能正常运行。
```
# cd /usr/bin
# ls -ol yum*
```
可以看到包括gnome-tweak-tool和urlgrabber在内共8个文件：
依次用vim打开每个文件
```
# vim yum
```

将文件头部 #!/usr/bin/python改为图中所示的样子，即加一个2，修改所有上面8个文件，保存关闭即可。对于vim的操作，i 插入模式，ESC退出，：末行模式，wq，保存退出。更详细的使用方法可自行找文档学习。

9，如果需要更换python的pip下载源，可以通过命令行临时指定，
```
# pip install packagename -i http://pypi.douban.com/simple
```
也可以添加配置文件，
```
# cd ~
# mkdir .pip
# cd .pip
# vim pip.conf
```
配置文件内容如下，源地址可以自行修改。
```
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
trusted-host = mirrors.aliyun.com
```
10，大功告成，后续可安装Pycharm，Atom等IDE，也可以pip安装python虚拟环境。
