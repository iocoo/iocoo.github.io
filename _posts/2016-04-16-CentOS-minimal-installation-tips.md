---
layout: post
title: CentOS Minimal 安装和配置TIPS
date: 2016-04-16 20:32:00 +0800
cacategory: Tech
author: GIGI WANG
tags: Linux
---
Linux 各种发行版虽然和Unix系非常相近，但还是有许多差别的。Linux更有趣吧.最近需要学习和测试，就在OSX平台下VirtualBox重新安装CentOS 7.由于机器本身性能原因，另外个人觉得Minimal安装不仅节约资源，而且可以更好的锻炼动手能力。再次把这次安装配置记录下来。

1.安装媒体介质是CentOS-7-x86_64-DVD-1511.iso（4.3GB.）
2.选择Minimal最小化安装。不包含KDE/GOME.
3.安装完成后进行配置，首先网络，试了ifconfig..（minimal不包含net-tools），需要自行安装，选择以CDROM（ISO）为安装源。
挂载光盘镜像：
<pre>
# mkdir -p /mnt/cdrom
# mount /dev/cdrom  /mnt/cdrom
</pre>
2.设置源
  为了使用光盘镜像安装源，配置yum.repo：
  暂时将/etc/yum.repos.d目录下文件备份，新建CentOS-Iso.repo,添加下面内容：
<code># vi CentOS-Iso.repo</code>
<pre>
[base]
name=iso
baseurl=file:///mnt/cdrom
gpgcheck=0
</pre>
3.安装设置网络
<code># yum install net-tools</code>
虚拟机设置NAT和HOST-ONLY两个网卡，配置静态IP，配置文件：
<code> # vim /etc/sysconfig/network-scripts/ifcfgX </code>
<pre>
YPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.56.100
NETMASK=255.255.255.0
NETWORK=192.168.56.0
IPV4_FAILURE_FATAL=no
NAME=enp0s3
UUID=ad60e4e1-c250-47f2-a0d2-fa1fbdbb0bd2
DEVICE=enp0s3
ONBOOT=yes
</pre>
配置好后重启网络,测试内外网是否正常。
<code># service network restart</code>
4.关闭防火墙,CentOS 7 不是直接设置iptables
<code> # systemctl stop firewalld.service && sudo systemctl disable firewalld.service</code>
5.安装一些必备的软件如wget，php等看个人需要。
6.安装pip 
<code ># wget https://bootstrap.pypa.io/get-pip.py <br /> # python get-pip.py</code>
7.设置时区和时间
查看当前时区 <code> date -R </code>
修改设置时区 <code>tzselect</code>
<code> #  cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime</code>
或在profile（/etc/bashrc）添加环境变量
<code> TZ='Asia/Shanghai'; export TZ</code> 
设置时间更新同步
<code> #  yum install -y ntpdate</code> 
<code> #  ntpdate us.pool.ntp.org</code> 
8.设置管理员sudoers
<code> # vim /etc/sudoers </code> 添加:
<pre>
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
gwang   ALL=(ALL)       ALL
</pre>
9.其它
终端ssh连接后警告：
<pre>
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[gwang@localhost ~]$ locale
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
LANG=en_US.UTF-8
LC_CTYPE=UTF-8
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
</pre>
修复：
<code>
sudo vi /etc/environment </code>
<pre>
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
</pre>
10.继续..
