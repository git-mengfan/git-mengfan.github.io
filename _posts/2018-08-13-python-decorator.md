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

### 装饰器有什么用
装饰器常用语插入日志、性能测试、事务处理、缓存、权限校验等场景，下面以实例讨论：
假设现在有一些基础接口，在使用基础功能的时候，只需要调用基础接口即可。比如：
```
def f1():
    print('f1')

def f2():
    print('f2')

def f3():
    print('f3')

f1()
f2()
f3()
```

在之前的情况，基础接口可以被任何人调用，但现在需要给这些基础接口加一些验证功能，使验证通过的人才可以调用这些基础接口。我们可以：
```
def f1():
    # 验证1
    # 验证2
    # 验证3
    print('f1')

def f2():
    # 验证1
    # 验证2
    # 验证3
    print('f2')

def f3():
    # 验证1
    # 验证2
    # 验证3
    print('f3')

f1()
f2()
f3()
```

我们也可以：
```
def check_login():
    # 验证1
    # 验证2
    # 验证3
    pass


def f1():

    check_login()

    print('f1')

def f2():

    check_login()

    print('f2')

def f3():

    check_login()

    print('f3')
```

以上两种方法都可以达到目的，但是我们需要修改每一个需要验证的基础接口，并且改动了函数的实现，假如某些接口我们又不需要校验，那么我们就有需要去修改这些基础接口，从而去掉校验的功能，虽然是可以达到目的，但是，但是对于崇尚“懒”的程序员说，这么多太费事，而且也不优雅。在开发过程中，有一种“开放封闭”原则：开放：对扩展开放，封闭：对已实现的功能代码快封闭。
我们通过装饰器，就可以优雅的达到添加验证的目的：
```
def w1(func):
    def inner():
        # 验证1
        # 验证2
        # 验证3
        func()
    return inner

@w1
def f1():
    print('f1')
@w1
def f2():
    print('f2')
@w1
def f3():
    print('f3')
```

以上，即是用装饰器的方法为基础函数添加验证功能的实现。

### 装饰器分析
以上面例子f1为例，python解释器从上到下解释代码，步骤如下：
1. def w1(func): => 将w1函数加载到内存
2. @w1

上面，@w1等价于 w1(f1),函数内部：
```
def inner():
    #验证 1
    #验证 2
    #验证 3
    f1()     
return inner
```

将执行完的w1函数返回值赋值给@w1下面的函数的函数名f1 即将w1的返回值再重新赋值给f1,在之后我们执行新的f1函数的时候，新的f1函数内部就会先执行验证，再执行原来的f1函数，完成在不改变原基础接口的基础上，添加验证功能，将来如果不需要验证，只需要把@w1去掉即可。

装饰器实例来自网课课件。

### 更多装饰器示例
#### 1. 无参数函数
```
from time import ctime, sleep

def timefun(func):
    def wrappedfunc():
        print("%s called at %s"%(func.__name__, ctime()))
        func()
    return wrappedfunc

@timefun
def foo():
    print("I am foo")

foo()
sleep(2)
foo()
```

上面代码可理解为：

foo = timefun(foo)
#foo先作为参数赋值给func后,foo接收指向timefun返回的wrappedfunc
foo()
#调用foo(),即等价调用wrappedfunc()
#内部函数wrappedfunc被引用，所以外部函数的func变量(自由变量)并没有释放
#func里保存的是原foo函数对象

#### 2. 带参数的函数
```
from time import ctime, sleep

def timefun(func):
    def wrappedfunc(a, b):
        print("%s called at %s"%(func.__name__, ctime()))
        print(a, b)
        func(a, b)
    return wrappedfunc

@timefun
def foo(a, b):
    print(a+b)

foo(3,5)
sleep(2)
foo(2,4)
```

#### 3. 不定长参数函数
```
from time import ctime, sleep

def timefun(func):
    def wrappedfunc(*args, **kwargs):
        print("%s called at %s"%(func.__name__, ctime()))
        func(*args, **kwargs)
    return wrappedfunc

@timefun
def foo(a, b, c):
    print(a+b+c)

foo(3,5,7)
sleep(2)
foo(2,4,9)
```

#### 4. 装饰器中有return
```
from time import ctime, sleep

def timefun(func):
    def wrappedfunc():
        print("%s called at %s"%(func.__name__, ctime()))
        func()
    return wrappedfunc

@timefun
def foo():
    print("I am foo")

@timefun
def getInfo():
    return '----hahah---'

foo()
sleep(2)
foo()

print(getInfo())
```

运行结果：
```
foo called at Fri Nov  4 21:55:35 2016
I am foo
foo called at Fri Nov  4 21:55:37 2016
I am foo
getInfo called at Fri Nov  4 21:55:37 2016
None
```

如果修改装饰器为return func(), 运行结果为：
```
foo called at Fri Nov  4 21:55:57 2016
I am foo
foo called at Fri Nov  4 21:55:59 2016
I am foo
getInfo called at Fri Nov  4 21:55:59 2016
----hahah---
```

### 小结
以上内容为自己在学习过程中的笔记总结，在其中加入了自己的部分理解，感觉装饰器可以极大提高代码结构的简洁性和重复性。
