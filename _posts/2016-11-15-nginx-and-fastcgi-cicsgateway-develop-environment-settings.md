---
layout: post
title: ngnix and fastcgi and cicstg 开发环境搭建
date: 2016-11-15 10:20:00 +0800
cacategory: c
author: GIGI WANG
tags: c
---

<h3>ngnix and fastcgi and cicstg 开发环境搭建</h3>
  一个搭建HTTP Server的项目需求，虽然方案没有最终确定，使用Ngnix做代理转发，后端使用使用开发语言无关的CGI应用进行业务逻辑处理，初步技术是可行的。但使用C语言开发WebServer 应用或许真不是什么好主意，那这样想啊，我们还用CICS这么古老的东西呢？--这里又忍不住想吐槽cics了..这个架构中的毒瘤！....
  进入正题吧，首先安装ngnix，ngnix的强大就不用叨叨了，安装也相当顺利。
  
1. 下载安装PCRE 
 
 <pre class="brush:bash"># wget  ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.39.tar.gz
 # ./configure;make;make install;
 </pre>
 
2. 下载安装nginx->[[这里]](http://nginx.org/)

 <pre class="brush:bash"># wget http://nginx.org/download/nginx-1.10.2.tar.gz
 # ./configure;make;make install;
 </pre> 
这样就可以启动nginx，默认安装在了```/usr/local/nginx```目录下.

3. 下载安装spawn-fcgi,可以从Github Lighthtpd项目中下载，也可以直接从[lighttpd.net这里](http://blog.lighttpd.net/articles/2009/09/23/spawn-fcgi-1-6-3-released/)下载。同样可以编译出```Spawn-fcgi```这个可执行程序。ngnix是支持FastCgi的，但是没有这个CGI管理程序。
4. 接下来就可以写CGI程序了，可以使用一些现成开源库，当然也可以自己写。
5. 安装CICS TRANSACTION GATEWAY，!!!NOTE!!![这玩意基本上支持32系统，安装时也会各种水土不服的，你可能需要32位的jre..]安装开发中可能需要32位支持，比如<pre class="brush:bash">libXp.i686 
glibc-devel.i686</pre>--慢慢享受其中的酸爽吧%$%$%.

6. 顺利的话应该可以写代码测试一下了。以下代码片段是接受从浏览器提交的表单然后提交CTG请求，编写代码编译。
 <pre class="brush:c"> while (FCGI_Accept() >= 0) {   
        memset(sBufIn,0,sizeof(sBufIn));
        FCGI_fread(sBufIn, sizeof(char), sizeof(sBufIn), FCGI_stdin);
        if(strlen(sBufIn)==0)
        {
            memcpy(sBufIn,sParmas,strlen(sParmas));
        }
        memset(&tHttpIn,0x00,sizeof(HttpReqT));
        rc=InitHttpIn(&tHttpIn,sBufIn);
        resultSize= URLDecode(tHttpIn.MsgIn, tHttpIn.MsgIn, sizeof(tHttpIn.MsgIn));
        rc=CallCtgSvr(&tHttpIn);
        if(rc != RC_OK)
        {
            printf("Content-type: text/plain\r\n"
                "\r\n"
                ""
                "QUERY_STRING :%s.\n"
                "REQUEST_METHOD : %s.\n"
                "CONTENT_TYPE : %s.\n"
                "CONTENT_LENGTH : %s.\n"
                "SERVER_PROTOCOL : %s.\n"
              ...
         }
         ...
     }
     // CTG 调用函数
     // CTG_openRemoteGatewayConnection();
     // CTG_ECI_Execute(gatewayToken, &eciParms);
     // Makefile libs -m64 -lctgclient -lfcgi
    </pre>

7. 配置nginx 端口转发处理FASTCGI.
 <pre class="brush:bash">
 location ~ \.cgi$ {
        fastcgi_pass 127.0.0.1:8000;
        fastcgi_index index.cgi;
        fastcgi_param SCRIPT_FILENAME fcgi$fastcgi_script_name;
        include fastcgi_params;
    }</pre>
8. 配置nginx 端口转发处理FASTCGI.启动CGI程序。  
    <pre class="brush:bash"> /usr/local/nginx/sbin/spawn-fcgi -a 127.0.0.1 -p 8000 -f /usr/local/nginx/cgi-bin/ctgcls </pre>
9. 使用浏览器测试或其他HTTP 客户端程序测试 <pre class="brush:python">#!/usr/bin/env python
#coding=utf8
import httplib, urllib
httpClient = None
try:
    #params = urllib.urlencode({'name': 'tom', 'age': 22})
    params = '''TEST CTG..'''
    headers = {"Content-type": "application/x-www-form-urlencoded", "Accept": "text/plain"}
    httpClient = httplib.HTTPConnection("192.168.101.181", 80, timeout=30)
    httpClient.request("POST", "/ctgcls.cgi", params, headers)
    response = httpClient.getresponse()
    print response.status
    print response.reason
    print response.read()
    print response.getheaders() #获取头信息
except Exception, e:
    print e
finally:
    if httpClient:
        httpClient.close() 
</pre> 

 
 