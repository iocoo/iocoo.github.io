---
layout: post
title: Default Parameter Values in Python
date: 2016-04-28 20:32:00 +0800
cacategory: coding
author: GIGI WANG
tags: python
---
[翻译->原文在这里](http://effbot.org/zone/default-values.htm)


<h3>Python中的默认参数值</h3>

Python中默认参数值的处理是一些容易绊倒大多数Python新手程序员的事情之一（但通常只会有一次）。
造成这种困惑的原因是当使用一个可变的对象作为默认值,也就是在某些地方可以被修改的一个值，比如一个list或一个字典。

一个例子:
<pre>
>>> def function(data=[]):
...     data.append(1)
...     return data
...
>>> function()
[1]
>>> function()
[1, 1]
>>> function()
[1, 1, 1]
</pre>

正如你看到的,这个list变得越来越长。如果查看这个list的identity，会发现函数原来一直返回同一对象：
<pre>
>>> id(function())
12516768
>>> id(function())
12516768
>>> id(function())
12516768
</pre>

这个原因很简单:函数在每次调用中一直使用同一的对象。我们做的修改有“粘性”。
这是什么鬼?#
当其所属的"def"语句被执行，默认参数值就会确定,参看:
http://docs.python.org/ref/function.html (挂了)
Python语言参考相应内容

同样注意到“def”是python中一个可执行语句,而且默认参数在“def”语句的环境中被赋值.如果多次执行"def"语句，它将每次创建一个新的函数对象(包括重新计算默认值)。
我们来看下面的例子。

What to do instead? #
该怎么做呢？#
解决方法是这样的,如别人提过的，使用一个占位符,而不是修改默认值。None 就是一个普通值：
<pre>
def myfunc(value=None):
    if value is None:
        value = []
    # modify value here
</pre>
如果需要处理任意对象(包括None),可以使用sentinel对象：
<pre>
sentinel = object()
def myfunc(value=sentinel):
    if value is sentinel:
        value = expression
    # use/modify value here
</pre>
在稍旧的代码中,在"object"采用之前，有时可以看到像这样语句
<code>sentinel = ['placeholder']</code>被用来创建一个拥有唯一标识的Non-false对象;[]每次赋值时都创建一个新的list。
正确使用可变默认值#
最后,应该注意到,更多的高级Python代码常常运用这一机制的优势;例如,当在一个循环中创建一批UI按钮，可以尝试这样：
<pre>
for i in range(10):
    def callback():
        print "clicked button", i
    UI.Button("button %s" % i, callback)
</pre>
所有的callback函数打印相同值(这种情况下很可能是9).原因是Python嵌套作用域绑定变量,而不是对象值,因此所有的callback实例都会显示变量“i”的当前值。
使用explicit绑定来修复这一问题：
<pre>
for i in range(10):
    def callback(i=i):
        print "clicked button", i
    UI.Button("button %s" % i, callback)
</pre>
“i=i” 绑定参数“i”(本地变量)到当前外部变量“i”的当前值。
两种其它使用:本地缓存/记忆使用;例如
<pre>
def calculate(a, b, c, memo={}):
    try:
        value = memo[a, b, c] # return already calculated value
    except KeyError:
        value = heavy_calculation(a, b, c)
        memo[a, b, c] = value # update the memo dictionary
    return value
</pre>
(这对于某些特定的递归算法特别好)
而且,为了更加优化的代码,使用全局名称的本地重绑定:
<pre>
import math
def this_one_must_be_fast(x, sin=math.sin, cos=math.cos):
    ...
</pre>
细节上是怎么处理的呢？
 当Pyhton执行“def”语句，它会产生一些就绪的片（包括已编译的函数体代码和当前命名空间），创建一个新的函数对象，完成后默认值也将确定。
一系列不同的可用组件作为函数对象的属性，上面用到的函数:
<code>
>>> function.func_name
'function'
>>> function.func_code
<code object function at 00BEC770, file "<stdin>", line 1>
>>> function.func_defaults
([1, 1, 1],)
>>> function.func_globals
{'function': <function function at 0x00BF1C30>,
'__builtins__': <module '__builtin__' (built-in)>,
'__name__': '__main__', '__doc__': None}
</code>
因为你可以访问默认参数，可以修改它们:
<code>
>>> function.func_defaults[0][:] = []
>>> function()
[1]
>>> function.func_defaults
([1],)
</code>
然而，这不是我想要推荐的常规使用方法.
另外一种重置默认方法是 简单地重新执行同一"def"语句,Python 创建一个新的代码对象绑定，确定默认值，像之前一样分配函数对象到相同的变量。
你需要明确你在做什么然后再这样做。
是的，如果碰巧不是这个函数，可以使用函数类的新模块中创建自己的函数对象。