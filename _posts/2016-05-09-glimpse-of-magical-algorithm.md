---
layout: post
title: 瞧瞧这神奇的算法
date: 2016-05-09 16:12:00 +0800
cacategory: algorithm
author: GIGI WANG
tags: algorithm
---

Algorithms are so magical. This is only the tips of the iceberg.
坚持每天记录一点儿，把丢掉的找回来...

<h3>1.欧几里得算法,求最大公约数</h3>
  小学数学就学过的，但不是这种算法。详细的描述参考Wikipedia.
  [WIKI:辗转相除法](https://zh.wikipedia.org/wiki/%E8%BC%BE%E8%BD%89%E7%9B%B8%E9%99%A4%E6%B3%95)


 Go语言的实现：
 （1）循环
<pre>
func gcd(x,y int) int {
    for y!=0 {
        x,y=y,x%y
    }
    return x
}
</pre>
 （2）递归
<pre>
func gcd(x,y int) int {
    if y==0 {
        return x
    }else {
       return  gcd(y,x%y)
    }
}
</pre>
<h3>2.斐波那契数列</h3>
学习C语言时肯定接触过。
[WIKI:斐波那契数列](https://zh.wikipedia.org/wiki/%E6%96%90%E6%B3%A2%E9%82%A3%E5%A5%91%E6%95%B0%E5%88%97)

斐波那契数列(意大利语：Successione di Fibonacci），又译费波拿契数、斐波那契数列、费氏数列、黄金分割数列。在数学上，费波那契数列是以递归的方法来定义：

- F_0=0
- F_1=1
- F_n = F_{n-1}+ F_{n-2}（n≧2）

当年教程中的写法：
<pre>
func fibs (n int) int {
    if n==0 {
       return 0
    }
    if n==1 {
      return 1
    }
    if n>1 {
      k := fibs(n-1)+fibs(n-2)
      return k
    }
    return -1;
}
</pre>
这个算法时间复杂度O(?),没搞明白..是相当恐怖的。下面的使用数组改善算法：
<pre>
func fibs(n int) int {
    x, y := 0, 1
    for i := 0; i < n; i++ {
        x, y = y, x+y
    }
    return x
}
</pre>
其它的通项公式法这里就不再讨论...
