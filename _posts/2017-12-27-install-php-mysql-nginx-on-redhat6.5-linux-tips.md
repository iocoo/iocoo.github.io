---
layout: post
title: nginx php5.3+mysql5.1 redhat6.5 配置记录
date: 2017-12-27 10:30:00 +0800
cacategory: linux
author: GIGI WANG
tags: nginx;php
---


一台没有联网但是有安装介质的rhel6.5-x86_64服务器。
计划安装部署上php+mysql 服务。由于有安装介质，光盘上的mysql版本5.1，php 版本5.3.3
，没办法软件版本老就老了凑合用了。

1.首先挂载上iso光盘

```bash
   # mount -o loop -t iso9660 /csys/rhel-server-6.5-x86_64-dvd.iso  /mnt/vcdrom/
```

2.安装 php mysql

```bash
    # cd /mnt/vcdrom/Packages/
    # ls -rlt php* mysql*
    # rpm -ivh mysql*.rpm --nodeps --force
    # rpm -ivh php*.rpm --nodeps --force
```

3.编译安装nginx
  nginx 安装没什么说的，源码nginx-1.12.2编译安装。

```bash 
    # ./configure
    # make & make install
```

4. 安装一些php依赖包，rhel5.6介质中的包很少的，基本的php-fpm也是没有的。
   可以从以下网站获取rpm包：
    https://www.rpmfind.net
    https://pkgs.org/download 
    http://rpm.pbone.net/

 ```bash
    php-fpm-5.3.3-26.el6.x86_64.rpm
    php-mbstring-5.3.3-26.el6.x86_64.rpm
    php-mcrypt-5.3.3-5.el6.x86_64.rpm
    libmcrypt-2.5.8-9.el6.x86_64.rpm```
 ```

5. nginx.conf 配置

```
    server {
            listen       80;
            server_name  localhost;
            location / {
                root   html;
                index  index.html index.htm index.php;
            }
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        
            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
            location ~ \.php$ {
                root           html;
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                include        fastcgi_params;
        }
```

6. php 配置
  编辑/etc/php.ini

```session.save_path = "/var/lib/php/session"
      session.auto_start = 1
```
​```
      chmod -R 777 /var/lib/php
```

7.设置服务开机启动

```
   # chkconfig php-fpm on
   # chkconfig mysqld on
```

8.启动服务

```bash
   # service mysqld start 
   # service php-fpm start
   # /usr/local/nginx/sbin/nginx   
   # mysqladmin -u root password ‘passwd’
```

9.验证nginx以及php服务
```
   # echo “<?php  echo phpinfo();?>”  > /usr/local/nginx/html/info.php
```

  打开浏览器输入http://REMOTE/
  打开浏览器输入http://REMOTE/info.php

10.安装phpMyAdmin 
  下载并解压phpMyAdmin-4.0.10.20-all-languages.tar.gz，建立到/usr/local/nginx/html/的软连接。
  注意phpMyAdmin 支持的php版本。