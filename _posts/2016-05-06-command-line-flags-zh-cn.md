---
layout: post
title: Go语言示例:命令行参数
date: 2016-05-06 15:32:00 +0800
cacategory: go
author: GIGI WANG
tags: go
---
[sc_url]:https://gobyexample.com/
原文: [Go by Example:Command-Line Flags][sc_url]

命令行标识（参数）是一种常用的指定命令行程序选项的方法，例如，在`wc -l`中 “-l” 就是一个命令的参数。
Go提供了一个支持基本的命令行参数解析的flag package（包）。我们将使用这个package来实现我们的命令行程序示例。

<pre>
package main

import "flag"
import "fmt"

func main() {

    // 基本的标识可以声明为string,integer以及bool类型。
    // 这里我们什么了一个字符串参数word，它有一个默认值“foo”，
    // 还有一个简短的描述。
    // flag.String函数返回一个字符串指针（不是字符串值）；
    // 下面将看到如何来使用这个指针。
    
    wordPtr := flag.String("word", "foo", "a string")

    // 类似于前边的'word' flag，这里声明了numb和fork标识.
 
    numbPtr := flag.Int("numb", 42, "an int")
    boolPtr := flag.Bool("fork", false, "a bool")

    // 还可以使用在程序成任何地方声明过的var变量来声明一个选项。 
    // 注意，我们需要为函数传递指向这个flag的指针。
    
    var svar string
    flag.StringVar(&svar, "svar", "bar", "a string var")

    // 一旦所有的声明完成，还需要调用flag.Parse()来执行命令行解析。
    
    flag.Parse()

    // 接下来，我们打印输出解析的选项还有其它紧接位置的参数。
    // (注:Trailing positional arguments怎么翻译呢?)
    // 还要注意，我们需要用形如*wordPtr的指针指向值，来获取参数实际值。
    
    fmt.Println("word:", *wordPtr)
    fmt.Println("numb:", *numbPtr)
    fmt.Println("fork:", *boolPtr)
    fmt.Println("svar:", svar)
    fmt.Println("tail:", flag.Args())
}
</pre>

最好先编译下，然后直接运行生成二进制程序来测试这个带参数的命令行程序。
<pre>
$ go build command-line-flags.go
</pre>

首先用所有flag都有值来测试下这个编译好的程序。
<pre>
$ ./command-line-flags -word=opt -numb=7 -fork -svar=flag
word: opt
numb: 7
fork: true
svar: flag
tail: []
</pre>
注意，忽略的flag参数将被自动赋予他们的默认值。
<pre>
$ ./command-line-flags -word=opt
word: opt
numb: 42
fork: false
svar: bar
tail: []
</pre>

紧随参数可以为提供给任意flag
<pre>
$ ./command-line-flags -word=opt a1 a2 a3
word: opt
...
tail: [a1 a2 a3]
</pre>

注意，flag package需要所有的flag都要在位置参数之前出现（否则这些flag将会被位置参数中断调掉）
<pre>
$ ./command-line-flags -word=opt a1 a2 a3 -numb=7
word: opt
numb: 42
fork: false
svar: bar
trailing: [a1 a2 a3 -numb=7]
</pre>

使用 -h 或者 --help 参数可以自动为命令行程序生成帮助信息
<pre>
$ ./command-line-flags -h
Usage of ./command-line-flags:
  -fork=false: a bool
  -numb=42: an int
  -svar="bar": a string var
  -word="foo": a string
 </pre>
 
如果提供的flag不是falg package中指定。程序将打印出错信息并显示帮助文本。
<pre>
$ ./command-line-flags -wat
flag provided but not defined: -wat
Usage of ./command-line-flags:
...
</pre>

接下来我们将看到环境变量-另一种常用参数化程序方法。
