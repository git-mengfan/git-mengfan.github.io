---
layout: post
title: Ubuntu18.04升级Pip出错解决办法
categories: Blog
description: Ubuntu18.04升级Pip后使用Pip出错问题修复
keywords: pip， Ubuntu
---

Ubuntu18.04默认自带Python3.6.7，但不自带pip3，需要apt安装，直接输入
```
sudo apt install python3-pip
```

查看Pip版本
```
Pip3 -V
```
发现版本9.0.1，当前最新的版本已经是20.0.3了，所以如果直接python更新
```
python3 pip install --upgrade pip
```
提示更新完成，但是再次查看版本的时候报错。
解决办法：
```
$ cd /usr/bin
$ sudo vi pip3
```
将如下内容：
```
from pip import main
if __name__ == '__main__':
    sys.exit(main())
```
修改为：
```
from pip import __main__
if __name__ == '__main__':
    sys.exit(__main__._main())
```
