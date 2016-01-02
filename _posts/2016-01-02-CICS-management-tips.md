---
layout: post
title: CICS 配置管理
date: 2016-01-02 09:18:00 +0800
cacategory: Tech
author: GIGI WANG
tags: cics
---

###配置CICS###

####配置 CICS REGION:####

	cicscp -v start sfs_server SFS_NAME	
	cicscp -v create region REGION_NAME DefaultFileServer=

####配置 RD:####

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

####配置 LD：####

	cicsadd -c ld -r REGION_NAME LD1 TCPAddress="backend_server" TCPService="tran-host" SNAServerTransport=TCP

其中 backend_server 是后端服务器的 IP 地址。
添加以下行以编辑 `/etc/services` 文件：

	tran-host port_number/tcp

其中 `port_number` 是端口号。请确保此端口号与 `/etc/services` 文件中的其他端口号不冲突。
####配置环境变量：####
添加以下行,编辑<code>/var/cics_regions/REGION_NAME/environment</code>

	COBPATH=/user/bin:/usr/lpp/cobol/bin:/etc:/usr/sbin:/usr/ucb:$HOME/bin:/usr/bin/X11
	CICS_PROGRAM_PATH=/home/cbod/cbodload/load:/usr/lpp/cics/lib:.
	DB2INSTANCE=db2inst1
	COB_CPM_SIZE_MAX=0
	EXTSHM=ON

####配置 WD：####

	cicsupdate -c wd -r REGION_NAME MDCL Permanent=no RecoverTerminal=no
	cicsupdate -c wd -r REGION_NAME MDCI Permanent=no RecoverTerminal=no

####配置 TD:####
	cicsadd -c td -r REGION_NAME TCCZ ProgName="DFHMIRS" Timeout=5 TWASize=32767
	cicsadd -c td -r REGION_NAME INQY ProgName="GCCBINQY" Timeout=5 TWASize=32767
	cicsadd -c td -r REGION_NAME TCCA ProgName="GCCBMMN" Timeout=5 TWASize=32767

####配置 PD:#####
	cicsadd -c pd -r REGION_NAME GCCBMMN PathName="GCCBMMN" RSLKey=public
	cicsadd -c pd -r REGION_NAME GCCBINQY PathName="GCCBINQY" RSLKey=public
	cicsadd -c pd -r REGION_NAME GCCBCOP PathName="GCCBCOP" RSLKey=public

####配置 XAD####

	cicsadd -c xad -r REGION_NAME cicsdb2 SwitchLoadFile="/usr/lpp/cics/bin/db21pc" XAOpen="DBNAME,USER_ID,PASSWORD"

其中 REGION_NAME、DBNAME、USER_ID 和 PASSWORD 需相应地替换为您环境中的信息。
####配置 CTG####
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

####停止CICS####
sudo cicscp -v stop region RG02
####启动CICS####
sudo cicscp -v start region RG02 StartType=cold



