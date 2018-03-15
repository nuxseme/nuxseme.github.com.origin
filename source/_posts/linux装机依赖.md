---
title: linux装机依赖
categories:
  - linux
date: 2018-03-15 17:13:03
---


### nginx 依赖
```
yum -y install pcre-devel openssl openssl-devel openssh-clients gcc automake autoconf libtool make curl libcurl-devel expat-devel perl-ExtUtils-MakeMaker gpm-libs
```

### win上传下载

```
yum -y install lszrz
```

### wget

    yum -y install wget

### w3m

	wget https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
	wget http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/w/w3m-0.5.3-36.git20180125.el7.x86_64.rpm
    wget http://rpmfind.net/linux/epel/7/x86_64/Packages/p/perl-NKF-2.1.3-5.el7.x86_64.rpm
  	//错误：依赖检测失败：
	//libgc.so.1()(64bit) 被 w3m-0.5.3-36.git20180125.el7.x86_64 需要
	//perl(NKF) 被 w3m-0.5.3-36.git20180125.el7.x86_64 需要
	wget https://rpmfind.net/linux/centos/7.4.1708/os/x86_64/Packages/gc-7.2d-7.el7.x86_64.rpm
	
### net-tools
	
	yum install -y net-tools
	
	
