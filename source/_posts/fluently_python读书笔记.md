---
title: fluently_python读书笔记
date: 2018-05-28 18:31:24
type: "tags"
tags:
    - python
    - 流畅的python
comments: True
---
## 1.第一个python问题 
流畅的python里面14章  可迭代的对象，迭代器，生成器里面，最后一个延伸阅读，里面列举了这样一个例子。

```
def f(): x=0
    while True:
        x += 1
        yield x
```
论文的作者指出，我们无法通过函数调用抽象产出这个过程。
然后论文的作者做了一个变化，变成下面的例子来简单地抽象产出这个过程。

```
def f():
    def do_yield(n):
        yield n
    x = 0
    while True:
        x += 1
        do_yield(x) 
```
这样会得到一个无限的循环，而不是生成器。

如果调用示例 14-24 中的 f(),会得到一个无限循环,而不是生成
器,因为 yield 关键字只能把最近的外层函数变成生成器函数。
虽然生成器函数看起来像函数,可是我们不能通过简单的函数调用
把职责委托给另一个生成器函数。
<!--more-->
而必须使用yield from 才能解决这个问题。

```
def f():
    def do_yield(n):
        yield n
    x = 0
    while True:
        x += 1
        yield from do_yield(x)
```
然后作者后面列举了一个斐波那契数列，实现如下：

```
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b
```
这个和上面第一个列举的例子有什么区别？
而且yield from 和 yield 在这个里面的区别在哪？


## 第二个问题：
猴子补丁问题。（第15章，上下文管理器和else块）

```
class LookingGlass:
    def __enter__(self): ➊
        import sys
        self.original_write = sys.stdout.write ➋
        sys.stdout.write = self.reverse_write ➌ # 在这个地方使用猴子补丁，替换成自己编写的方法。
        return 'JABBERWOCKY' ➍
        
    def reverse_write(self, text): ➎
        self.original_write(text[::-1])
        
    def __exit__(self, exc_type, exc_value, traceback):
        import sys ➐
        sys.stdout.write = self.original_write ➑
        if exc_type is ZeroDivisionError: ➒
            print('error')
```
在➌处使用了猴子补丁，在python3里面运行没有问题，但是放到python2里面，会报write方法是只读的？ 原因是因为python2和python3,write方法不一样？还是python2不支持猴子补丁？


python2会报如下错误:
```
AttributeError: 'file' object attribute 'write' is read-only
```


