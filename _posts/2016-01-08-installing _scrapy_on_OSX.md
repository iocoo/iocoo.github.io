---
author: GIGI WANG
title: Install Scrapy on OSX
layout: post
date: 2016-01-08 16:35:00 +0800
category: Tech
tags: python,Scrapy
---
顺利的话，一句命令搞定：
`sudo pip install Scrapy`
OSX还是需要额外一些包或升级的，包括：

	cssselect, queuelib, six, w3lib, lxml, Twisted, characteristic, pyasn1, pyasn1-modules, service-identity

幸运的是pip或者easy_install 可以自动为你安装，但是其中的问题：
>Found existing installation: six 1.4.1
    DEPRECATION: Uninstalling a distutils installed project (six) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.
    Uninstalling six-1.4.1:
    
...

>OSError: [Errno 1] Operation not permitted: '/tmp/pip-qeBchm-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six-1.4.1-py2.7.egg-info'

显然升级six是出错了,使用sudo
`pip install six --upgrade` 或者 `pip uninstall six`
都会遇到同样的错误。无论什么用户，多大权限。想必下载包安装也会同样问题。我只能这么认为：这都是pip的错。
好吧，那就easy_install试试吧。

>sudo easy_install --upgrade  six
>Searching for six
>Reading https://pypi.python.org/simple/six/
>Best match: six 1.10.0
>Downloading https://pypi.python.org/packages/source/s/six/>six-1.10.0.tar.gz#md5=34eed507548117b2ab523ab14b2f8b55
>Processing six-1.10.0.tar.gz

>###....

>Installed /Library/Python/2.7/site-packages/six-1.10.0-py2.7.egg
>Processing dependencies for six
>Finished processing dependencies for six

继续使用easy_install 安装Scarpy吧:...

	sudo easy_install Scrapy
看起来挺顺利..
可是....
>In file included from src/lxml/lxml.etree.c:323:
>src/lxml/includes/etree_defs.h:14:10: fatal error: 'libxml/xmlversion.h' file not found
>\#include "libxml/xmlversion.h"
>        ^
>1 error generated.
>Compile failed: command 'cc' failed with exit status 1
/tmp/easy_install-U7v3Lb/lxml-3.5.0/temp/xmlXPathInitxO27oS.c:1:10: >fatal error: 'libxml/xpath.h' file not found
>\#include "libxml/xpath.h"
>         ^
>1 error generated.

看来libxml2 需要额外安装的

	xcode-select --install
	
以下就顺利完成安装

	sudo pip install Scrapy
	OR
	sudo pip easy_install Scrapy


