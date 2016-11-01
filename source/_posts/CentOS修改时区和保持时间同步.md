---
title: CentOS修改时区和保持时间同步
categories:
  - centos
date: 2016-09-24 14:15:03
---


### 修改时区

	cp -y /usr/share/zoneinfo/xxx|xxx  /etc/localtime
	#复制选择的时区到/etc/localtime 下覆盖

	//或者建立软连接
	ls -n /usr/share/zoneinfo/xxxx  /etc/localtime
	
### 更新时间

	yum list  ntp
	yum install ntp.x86_64
	ntpdate time.nist.gov 

### 定时任务更新时间
    
    /10 * * * * ntpdate time.nist.gov   #域名或IP  
	每隔十分钟同步一次。推荐几个时间服务器。
	time.nist.gov
	time.nuri.net
	asia.pool.ntp.org
	asia.pool.ntp.org
	asia.pool.ntp.org
	asia.pool.ntp.org