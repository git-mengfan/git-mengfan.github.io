---
layout: post
title: Python装饰器
categories: Blog
description: Python装饰器笔记
keywords: Python，装饰器
---
### 什么是Python装饰器
首先要明白，在Python中，函数也是对象，Python中的函数可以像普通变量一样当做参数传递给另外一个函数。比如：
```
In [1]: def hello():
   ...:     print('hello')
   ...:                                                                         

In [2]: a = hello                                                               

In [3]: a()                                                                     
hello

```

其次要明白闭包：在函数内部再定义一个函数，并且这个函数用到了外边函数的变量，那么将这个函数以及用到的一些变量称之为闭包。比如：
```
In [5]: def test(number):
   ...:     def test_in(number_in):
   ...:         print("in test_in 函数, number_in is %d" % number_in)
   ...:         return number + number_in
   ...:     return test_in
   ...:                                                                         

In [6]: ret = test(10)                                                          

In [7]: print(ret(100))                                                         
in test_in 函数, number_in is 100
110

In [8]: print(ret(200))                                                         
in test_in 函数, number_in is 200
210

```
例子中内部函数test_in就称为闭包，再举个例子如下：
```
def line_conf(a, b):
    def line(x):
        return a*x + b
    return line

line1 = line_conf(1, 1)
line2 = line_conf(4, 5)
print(line1(5))
print(line2(5))
# 本例子来自网课内容
```
这个例子中，函数line与变量a，b构成闭包。在创建闭包的时候，我们通过line_conf的参数a，b说明了这两个变量的取值，这样，我们就确定了函数的最终形式(y = x + 1和y = 4x + 5)。我们只需要变换参数a，b，就可以获得不同的直线表达函数。在之后的调用中，我们只需传入一个参数。如果没有闭包，我们需要每次创建直线函数的时候同时说明a，b，x。

最后就是装饰器，可以认为返回值为另一个函数的函数，通常使用 @wrapper 语法形式来进行函数变换。装饰器本质上是一个Python函数，它可以让其他函数在不需要做任何代码变动的前提下增加额外功能。装饰器语法只是一种语法糖，以下两个函数定义在语义上完全等价：
```
def f(...):
    ...
f = staticmethod(f)

@staticmethod
def f(...):
    ...
# 本例子来自官方文档
```
所谓语法糖：计算机语言中添加的某种语法，这种语法对语言的功能没有影响，但是更方便程序员使用。

### 为什么要有装饰器
装饰器常用语插入日志、性能测试、事务处理、缓存、权限校验等场景，下面以实例讨论：
