---
layout: post
title: CICS 配置管理
date: 2016-01-02 09:18:00 +0800
cacategory: Tech
author: GIGI WANG
tags: cics
---

<h3>配置 CICS REGION:</h3>

	cicscp -v start sfs_server SFS_NAME	
	cicscp -v create region REGION_NAME DefaultFileServer=

<h3>配置 RD:</h3>

	cicsupdate -c rd -r REGION_NAME XPRecvTimeout=300 
	cicsupdate -c rd -r REGION_NAME MinServer=30 
	cicsupdate -c rd -r REGION_NAME MaxServer=80 
	cicsupdate -c rd -r REGION_NAME MaxRegionPool=62914560 
	cicsupdate -c rd -r REGION_NAME MaxTaskPrivatePool=20971520 
	cicsupdate -c rd -r REGION_NAME MaxTSHPool=41943040
	cicsupdate -c rd -r REGION_NAME TraceFlagMaster=on 
	cicsupdate -c rd -r REGION_NAME TraceFlagSystem=on 
	cicsupdate -c rd -r REGION_NAME TraceFlagUser=on AllowDebugging=yes

其中 REGION_NAME 需更改为您的 CICS REGION 名称。

<h3>配置 LD：</h3>

	cicsadd -c ld -r REGION_NAME LD1 TCPAddress="backend_server" TCPService="tran-host" SNAServerTransport=TCP

其中 backend_server 是后端服务器的 IP 地址。
添加以下行以编辑 `/etc/services` 文件：

	tran-host port_number/tcp

其中 `port_number` 是端口号。请确保此端口号与 `/etc/services` 文件中的其他端口号不冲突。

<h3>配置环境变量：</h3>

添加以下行,编辑<code>/var/cics_regions/REGION_NAME/environment</code>

	COBPATH=/user/bin:/usr/lpp/cobol/bin:/etc:/usr/sbin:/usr/ucb:$HOME/bin:/usr/bin/X11
	CICS_PROGRAM_PATH=/home/cbod/cbodload/load:/usr/lpp/cics/lib:.
	DB2INSTANCE=db2inst1
	COB_CPM_SIZE_MAX=0
	EXTSHM=ON

<h3>配置 WD：</h3>
  

	cicsupdate -c wd -r REGION_NAME MDCL Permanent=no RecoverTerminal=no
	cicsupdate -c wd -r REGION_NAME MDCI Permanent=no RecoverTerminal=no
配置WD

<h3>配置 TD:</h3>

	cicsadd -c td -r REGION_NAME TCCZ ProgName="DFHMIRS" Timeout=5 TWASize=32767
	cicsadd -c td -r REGION_NAME INQY ProgName="GCCBINQY" Timeout=5 TWASize=32767
	cicsadd -c td -r REGION_NAME TCCA ProgName="GCCBMMN" Timeout=5 TWASize=32767
TWASize 大小不能忽略。

<h3>配置 PD:</h3>

	cicsadd -c pd -r REGION_NAME GCCBMMN PathName="GCCBMMN" RSLKey=public
	cicsadd -c pd -r REGION_NAME GCCBINQY PathName="GCCBINQY" RSLKey=public
	cicsadd -c pd -r REGION_NAME GCCBCOP PathName="GCCBCOP" RSLKey=public
添加PD 查看和删除PD方法类似。


<h3>配置 XAD</h3>

	cicsadd -c xad -r REGION_NAME cicsdb2 SwitchLoadFile="/usr/lpp/cics/bin/db21pc" XAOpen="DBNAME,USER_ID,PASSWORD"

其中 REGION_NAME、DBNAME、USER_ID 和 PASSWORD 需相应地替换为您环境中的信息。

<h3>配置 CTG：</h3>
要配置 ctg.ini 文件，请在 `/opt/IBM/cicstg/bin/ctg.ini` 文件中添加以下行：

	SECTION SERVER = REGION_name
                UPPERCASESECURITY=N
                PROTOCOL=TCPIP
                NETNAME=IP_address
                PORT=port_number
                CONNECTTIMEOUT=0
                TCPKEEPALIVE=N
	ENDSECTION

- 其中 REGION_name、IP_address 和 port_number 需相应地替换为您环境中的信息。
- 使用 root 用户帐户完成以下步骤以重新启动 CTG：
    - 运行 cicscli -s 命令以启动 CTG。
    - 可选： 要验证是否已成功启动 CTG，请运行 cicscli -l 命令。

<h3>停启CICS</h3>

	cicscp -v stop region REGION_name
	cicscp -v start region REGION_name StartType=cold

<h3> TIPS</h3>	
IPC支持

	EXTSHM=ON
	
Db2中出现的超时产生core : Transaction ‘CPMI’, Abend ‘A147’, at ‘???’.

	Set the environment variable DB2NOEXITLIST=yes in DB2 client env.
   1.Set db2set DB2NOEXITLIST=yes.
   2.Terminate DB2 and restart the region.
   
	Set DB2NOEXITLIST=yes in region's environment

一些错误分析：
LINK和START调用的主要返回值解释如下：
1、LINK错误码分析
 
INVREQ(请求非法)
交易状态不对(有无SYNCONRETURN混用)
TRANSID全空
 
LENGERR
DATALENGTH选项为负值
DATALENGTH选项比LENGTH选项长
 
NOTAUTH
权限问题
使用了SYSID选项，但是RSLCheck没有设置为NONE
 
PGMIDERR
PD不存在
PD被disabled.
 
ROLLEDBACK
被LINK的程序无法执行syncpoint
 
SYSIDERR
CD不存在或错误
对方域不存在或已经宕机
网络不通
在本地TD:timeout时，远端交易还在队列
通信错误码：15a00002/15a00102
 
TERMERR
会话失败，TRANSID不存在
在本地TD:timeout时，远端交易还在运行。通信错误码：15a00007/a0000100
RD:MaxTClassLim引起的Reject。通信错误码：15a00007/84b6031
 
2、START错误码分析
 
INVREQ(请求非法)
Hours超范围
Minutes超范围
Seconds超范围
指定REQID但是该TSQ已经存在(在Pool不足时可能出现)
 
IOERR
SFS满
 
ISCINVREQ
ISC失败
 
LENGERR
使用LENGTH(0).
 
NOTAUTH
权限不足
使用了SYSID选项，但是RSLCheck没有设置为NONE
 
SYSIDERR
CD不存在或错误
对方域不存在或已经宕机
网络不通
通信错误码：15a00002/15a00102
 
TERMIDERR
TERMID选项非法
 
TRANSIDERR
TRANSID选项非法

-end



