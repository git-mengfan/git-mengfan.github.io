---
layout: post
title: Python生成器与迭代器
categories: Blog
description: 生成器与迭代器知识点学习笔记
keywords: Generator，Iterator
---
### 基本概念与关系
在Python中，聊到生成器和迭代器，一般都会涉及到以下几个概念，本来想着自己能用通俗的语言生动形象的把这几个概念解释清楚，但发现自己的理解也处于半吊子水平，所以还是结合文档说明更好一点。

#### 迭代：
1. 在Python中，比如有一个列表[1, 2, 3, 4, 5]，我们通过for循环逐个取出列表中元素的过程就叫迭代。
2. 这个和我们常说的软件迭代版本（指的就是在已有软件的基础上更新修复问题这个过程）有相似之处，但并不完全一样，要注意区别。

#### 迭代器协议：
1. 对象必须提供一个next方法，执行该方法要么返回迭代中的下一项，要么就引起一个Stopiteration异常，以终止迭代。

#### 可迭代对象：
1. 从实现来讲，就是对象内部定义一个__iter__方法的对象。
```
class collections.abc.Iterable
ABC for classes that provide the __iter__() method.
```
2. 通俗来讲，可以用直接用于for循环的对象统称为可迭代对象。
3. Python的数据结构中，列表，字典，元组，集合，字符串都是可迭代对象。
4. 生成器也是可迭代对象。
5. 判断是否为可迭代对象可以采用如下方法：
```
In [1]: a = (i for i in range(10))
In [2]: from collections import Iterable
In [3]: isinstance(a, Iterable)                                                 
Out[3]: True
```

#### 迭代器：
1. 从实现来讲，就是对象内部定义了 __iter__() and __next__() 的对象。
```
class collections.abc.Iterator
ABC for classes that provide the __iter__() and __next__() methods.
```
```
An object representing a stream of data. Repeated calls to the iterator’s __next__() method (or passing it to the built-in function next()) return successive items in the stream. When no more data are available a StopIteration exception is raised instead. At this point, the iterator object is exhausted and any further calls to its __next__() method just raise StopIteration again. Iterators are required to have an __iter__() method that returns the iterator object itself so every iterator is also iterable and may be used in most places where other iterables are accepted. One notable exception is code which attempts multiple iteration passes. A container object (such as a list) produces a fresh new iterator each time you pass it to the iter() function or use it in a for loop. Attempting this with an iterator will just return the same exhausted iterator object used in the previous iteration pass, making it appear like an empty container.
```
2. 可以被next()函数调用并不断返回下一个值的对象称为迭代器。
3. 判断方法如下：
```
In [56]: from collections import Iterator
In [57]: isinstance((x for x in range(10)), Iterator)
Out[57]: True
In [59]: isinstance({}, Iterator)
Out[59]: False
```
4. 生成器都是Iterator对象，但list，dict，str虽然是Iterable，却不是Iterator。
5. 迭代器只能往前不会后退。
6. 把list，dict，str等可迭代对象变成Iterator可以使用iter()函数。
```
In [62]: isinstance(iter([]), Iterator)
Out[62]: True
In [63]: isinstance(iter('abc'), Iterator)
Out[63]: True
```

#### 生成器：
1. 生成器是一种返回generator iterator的函数，它看起来，除了包含一个生成用在for循环中的一系列数或用在next方法中一次生成一个数的yield关键字表达式外，和普通函数并没有什么区别。
```
generator
A function which returns a generator iterator. It looks like a normal function except that it contains yield expressions for producing a series of values usable in a for-loop or that can be retrieved one at a time with the next() function.
Usually refers to a generator function, but may refer to a generator iterator in some contexts. In cases where the intended meaning isn’t clear, using the full terms avoids ambiguity.
```
```
generator iterator
An object created by a generator function.
```
2. 生成器是一类特殊的迭代器。
3. 生成器都是迭代器，迭代器则不一定式生成器。
4. 把一个列表生成式的 [] 改成 ()可以创建生成器。
```
In [15]: L = (x*2 for x in range(5))
In [16]: L
Out[16]: <generator object <genexpr> at 0x7f626c132db0>
In [17]: next(L)
Out[17]: 0
```
5. 构造带有yield表达式的函数也可以创建生成器。
```
In [19]: def fib():
    ...:        n = 0
    ...:        a, b = 0, 1
    ...:        while True:
    ...:             yield b
    ...:             a, b = b, a + b
    ...:             n += 1
    ...:                                                                       
In [20]: F =fib()                                                              
In [21]:next(F)                                                                
Out[21]: 1
In [22]:next(F)                                                                
Out[22]: 1
In [23]:next(F)                                                                
Out[23]: 2
```
6. 通过列表生成式，我们可以直接创建一个列表。但是如果需要创建一个包含特别多元素的列表，那么这个列表就会占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。如果我们通过生成器的方式，则可以在占用更少资源的情况下完成需求。
7. 生成器的return返回值无法通过正常方式获取，需要通过捕捉异常的方式获得。
8. send方法

#### 列表生成式：
1. 列表生成式为一个Python表达式，返回一个列表。
```
In [1]: L = [x*2 for x in range(5)]
In [2]: L
Out[2]: [0, 2, 4, 6, 8]
```
2. 语法结构为
```
[exp for iter in iterable]
```
3. 列表生成式可以嵌套，比如（输出格式已调整）：
```
In [7]: x = [1,2,3,4]                                                        
In [8]: y = [2,4,6,8]                                                        
In [9]: z = [(a, b) for a in x for b iny]                                      
In [10]:z                                                                      
Out[10]:
[(1, 2),(1, 4),(1, 6),(1, 8),(2, 2),(2, 4),(2, 6),(2, 8),
 (3, 2),(3, 4),(3, 6),(3, 8),(4, 2),(4, 4),(4, 6),(4, 8)]
```
相当于：
```
ret = []
for a in x:
      for b in y:
         ret.append(a, b)
```
4. 列表生成式其实就是一种根据已存在的可迭代对象生成新的列表的简洁写法，可作为代替循环赋值等操作的一种方式，参考上面的列子。
5. 与map，fitter等高阶函数的区别，Python2中好像效果一样，在Python3中，高阶函数的方式返回已不再是列表，而改为迭代器对象。map函数示例如下：
```
In [8]:  y = [2, 4, 6, 8]
In [12]: m = list(map(lambda i: i+1, y))
In [13]:m                                                                      
Out[13]: [3, 5, 7, 9]
In [14]:j=map(lambda i:i+1,y)                                                  
In [15]:j                                                                      
Out[15]: <map at 0x10443f910>
In [16]:next(j)                                                                
Out[16]: 3
```

### 备注：
