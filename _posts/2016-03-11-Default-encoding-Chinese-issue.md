---
layout: post
title: 汉字使用默认解码出现问题
date: 2016-03-11 17:32
cacategory: Coding
author: GIGI WANG
tags: coding
---
老生常谈的一个问题，汉字编码与解码问题，各种编码就不再复制粘贴了，需要的话维基百科捡起来再看看。

	名称		第一字节				第二字节
	GB2312	0xB0-0xF7(176-247)	0xA0-0xFE（160-254）
	GBK		0x81-0xFE（129-254）0x40-0xFE（64-254）
	
遇到一个问题，一字符串以‘|’ 分隔，解析后出现各种乱码。这显然是编码重叠导致的。
举例来看：
 "王文弢|赵珅|刘颖弢|田東|孙炜韡"
 查看这些汉字的编码，可以看到高位中正好有|这个相同编码。
 
那么就需要对汉字进行必要解码而不是默认。
<pre>
       unsigned char ch1 = (unsigned char) *s;
       unsigned char ch2 = (unsigned char) *(s+1);
       if (ch1>=129 && ch1<=254 && ch2>=64 && ch2<=254)
       {
        ...
       }

</pre>

查看其代码原来使用库函数strchr，于是重新写写这个函数：
<pre>
char *StrChr (char *s,char c)
{
   while(*s!='\0'&&*s!=c)
    {
       unsigned char ch1 = (unsigned char) *s;
       unsigned char ch2 = (unsigned char) *(s+1);
       if (ch1>=129 && ch1<=254 && ch2>=64 && ch2<=254)
       {
            ++s;
        }
        ++s;
    }
    return *s==c ? s: NULL;
}
</pre>

好了。以后再继续。
