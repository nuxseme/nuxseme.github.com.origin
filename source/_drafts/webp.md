---
title: webp
date: 2017-03-18 18:58:08
categories:	image
---

### webp简介
google开发的一种新的web图片格式，在保证清晰度的前提下非常有效减少图片的大小，降低请求响应时间，减少带宽

### 安装
#### 环境
- os: CentOS Linux release 7.2.1511 (Core)  
- webp[官网](https://developers.google.com/speed/webp/)
- webp[下载](https://storage.googleapis.com/downloads.webmproject.org/releases/webp/index.html) 
- [WebP 探寻之路](https://isux.tencent.com/introduction-of-webp.html) 


#### 安装流程
	$yum install libjpeg libpng libtiff libgif
	tar xvzf libwebp-0.6.0.tar.gz
	//解压完成之后不需要编译安装  复制目录到指定位置即可
	libwebp-0.6.0-linux-x86-64 ]$ ll
	total 56K
	drwxr-xr-x  6  100 users 4.0K Jan 31 04:42 .
	drwxr-xr-x 17 root root  4.0K Mar 18 19:38 ..
	drwxr-xr-x  2  100 users 4.0K Jan 31 04:42 bin
	drwxr-xr-x  2  100 users 4.0K Jan 31 04:42 doc
	drwxr-xr-x  3  100 users 4.0K Jan 31 04:42 include
	drwxr-xr-x  2  100 users 4.0K Jan 31 04:42 lib
	-rw-r--r--  1  100 users  22K Jan 31 04:42 README
	-rw-r--r--  1  100 users 7.6K Jan 31 04:42 README.mux
	
<!--more-->
### 使用
	cwebp [options] -q quality input.png -o output.webp
	
### 效果
	//由215K到60k -q 100   30K -q 50
	$ ll
	total 316K
	drwxr-xr-x  2 root root 4.0K Mar 17 18:37 .
	drwxr-xr-x 16 root root 4.0K Mar 17 18:33 ..
	-rw-r--r--  1 root root  60K Mar 17 18:37 cat100.webp
	-rw-r--r--  1 root root  23K Mar 17 18:36 cat50.webp
	-rw-r--r--  1 root root 215K May 25  2016 cat.png
