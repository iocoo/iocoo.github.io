---
layout: post
title: 关于学习python不得不说的
date: 2017-09-30 10:20:00 +0800
cacategory: python
author: GIGI WANG
tags: python,go
---

<h2>关于学习python不得不说的</h2>
<h3>--题外话</h3>

我还是推荐学习Go语言。<br/>
公司开始打算用python来做一些东西，而不再只是C。已经几年没有用python，再翻看Python恍如隔世，当年还是2.x的天下，python3还一直被认为不稳定，如今2.7都快out了，各种库越来越多，甚至杂乱臃肿，该怎么选择又成了困扰初学者的问题。pypi上的一些库也变得连他娘都不认识，不得不重新复习下,同时也和没接触过python的伙伴们分享一下。<br>
首先推荐以下一些有用的学习资源：
<h3>一些非常有用的资源</h3>

* 1.英文书籍[Free-programming-books](https://github.com/EbookFoundation/free-programming-books/blob/f2d40b8c613af08c03b535ca6de15a65f9856518/free-programming-books.md)
* 2.中文书籍[免费的编程中文书籍索引](https://github.com/justjavac/free-programming-books-zh_CN)
* 3.[*Github*](https://github.com/) [*stackoverflow*](https://stackoverflow.com/)
* 4.Git 入门书籍 [Git 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
* 5.官方原文文档
* 6.其它书籍和资料
  * [简明Python教程](https://bop.molun.net/) :新版4.0 python3
  * [简明Python教程](http://www.kuqin.com/abyteofpython_cn/index.html) :老版1.2 python2
  * [Google 开源项目风格指南-中文版](http://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/contents/)
  * [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)
  * [Twisted 与异步编程入门](https://www.gitbook.com/book/likebeta/twisted-intro-cn/details) 
  * [gevent-tutorial-ch](https://github.com/blurrcat/gevent-tutorial-ch/blob/master/tutorial.md)
   OR [gevent-tutorial](http://sdiehl.github.io/gevent-tutorial/)
  * [Requests: HTTP for Humans](http://requests-docs-cn.readthedocs.io/zh_CN/latest/)
  * [Tornado 4.3 文档中文版](https://tornado-zh.readthedocs.io/zh/latest/)
 
<h3>初识Python</h3>
--Python:  **{优雅 明确 简单}**<br/>

* Python 是什么?
>Python is an easy to learn, powerful programming language. It has efficient high-level data structures and a simple but effective approach to object-oriented programming. Python’s elegant syntax and dynamic typing, together with its interpreted nature, make it an ideal language for scripting and rapid application development in many areas on most platforms.

 * 设计哲学：“优雅 明确 简单”<br/>
Python开发者的哲学是“用一种方法，最好是只有一种方法来做一件事”*<br/>
在你的机器上运行<code>python</code>,敲如下命令看看吧：
```python
import this
```

  * Python 应用范围
 
  Web应用，测试工具，GUI开发,操作系统...
  * Why?

  为什么是Python呢?
  因为{优雅 明确 简单}，有活跃的社区，还有一个理由：别人都在用啊！
    * 2 or 3: 究竟选2呢还是3？，各有千秋吧。其实无所谓了，2.7.x虽然是最后一个2系，但可以兼容大多数3的语法，个人认为初学者可以选择任意新版本切入，入门后再自主切换吧。
    * 与C：动态特性；面向对象;具备垃圾回收,自动内存管理;高层次数据结构; 非常丰富+完备的标准库和第三方库
    * 与Go: Go具有python几乎所有优点，Golang性能更好，天生支持并发，丰富的标准库，编译部署简单。Python通过解释器执行使得性能不够理想，缺少类型检查导致代码质量难以保证，全局锁的存在限制了并发性能，此外缺乏对并发编程的良好支持。这些劣势正好是Go的优势。有兴趣还是推荐Go。
    * 与Perl:哲学思想：总有多种方法来做同一件事。烦烦繁繁！
    * GIT 
    * 自动化测试 网络编程
  
<h3>python 语法</h3>
<h4> 缩进 </h4>
 违反*缩进规则* 的程序无法编译通过；<br>
 *4个空格*表示每级缩进，避免使用Tab,避免使用标点，风格参照PEP 8规定和Goolge Python Stlye.
 <h4>语句和控制流</h4>
 
  * if语句，当条件成立时运行语句块。经常与else, elif（相当于else if）配合使用。
  * for语句，遍列列表、字符串、字典、集合等迭代器，依次处理迭代器中的每个元素。
  * while语句，当条件为真时，循环运行语句块。
  * try语句 与except, finally, else配合使用处理在程序运行中出现的异常情况。
  * class语句。用于定义类型。
  * def语句。用于定义函数和类型的方法。
  * pass语句。表示此行为空，不运行任何操作。
  * assert语句。用于程序调适阶段时测试运行条件是否满足。
  * with语句。Python2.6以后定义的语法，在一个场景中运行语句块。比如，运行语句块前加锁，然后在语句块运行结束后释放锁。
  * yield语句。在迭代器函数内使用，用于返回一个元素。自从Python 2.5版本以后。这个语句变成一个运算符。
  * raise语句。抛出一个异常。
  * import语句。导入一个模块或包。常用写法：from module import name, import module as name, from module import name as anothername

<h4>表达式</h4>

  Python的表达式写法与C/C++类似。只是在某些写法有所差别。
   * 主要的算术运算符与C/C++类似。 
   * Python使用```and, or, not```表示逻辑运算。
```is, is not```用于比较两个变量是否是同一个对象。```in, not in```用于判断一个对象是否属于另外一个对象。  
   * Python支持字典、集合、列表的推导式（dict comprehension, set comprehension, list comprehension）
  ```python
>>> [x + 3 for x in range(4)]
[3, 4, 5, 6]
>>> {x + 3 for x in range(4)}
{3, 4, 5, 6}
>>> {x: x + 3 for x in range(4)}
{0: 3, 1: 4, 2: 5, 3: 6}
```

* Python支持“迭代表达式”（generator comprehension），比如计算0-9的平方和：
```python
>>> sum(x * x for x in range(10))
285
```
* Python使用lambda表示匿名函数。匿名函数体只能是表达式。比如：
```python
>>> add = lambda x, y : x + y
>>> add(3, 2)
5
```

<h4>函数</h4>

Python的函数支持递归、默认参数值、可变参数、闭包，但不支持函数重载。 默认参数使用的一些陷阱注意理解下。

  >在 Python 源码中,我们使用def来定义函数或者方法。在其他语言中,类似的东西往往只是一一个语法声明关键字,但def却是一个可执行的指令。Python 代码执行的时候先会使用 compile 将其编译成 PyCodeObject.

  >PyCodeObject 本质上依然是一种静态源代码,只不过以字节码方式存储,因为它面向虚拟机。因此 Code 关注的是如何执行这些字节码,比如栈空间大小,各种常量变量符号列表,以及字节码与源码行号的对应关系等等。

  >PyFunctionObject 是运行期产生的。它提供一个动态环境,让 PyCodeObject 与运行环境关联起来。同时为函数调用提供一系列的上下文属性,诸如所在模块、全局名字空间、参数默认值等等。这是def语句执行的时候干的活。

  >PyFunctionObject 让函数面向逻辑,而不仅仅是虚拟机。PyFunctionObject 和 PyCodeObject 组合起来才是一个完整的函数。
    
<h4>面向对象和类</h4>

类似其它面向对象的语言特性，如C++，Java..
```python
#!/usr/bin/env python2.7
class Sample(object):
     def __init__(self):
         pass
     def Action(self,sth):
         pass
pass
 ```
<h4>数据类型与动态类型</h4>

Python采用动态类型系统。在编译的时候，Python不会检查对象是否拥有被调用的方法或者属性，直至运行时，才做出检查。所以操作对象时可能会抛出异常。不过，虽然Python采用动态类型系统，它同时也是强类型的。Python禁止没有明确定义的操作，比如数字加字符串。

<h4>标准库</h4>
Python 拥有强大而且完备的标准库，而且还在不断发展。另外还有庞大丰富的第三方库，可以很方便的获取和使用。

<h3>继续探索...</h3>
搜索 and 发现...