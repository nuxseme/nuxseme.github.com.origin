---
title: apache编译安装
categories:
  - Apache
date:
---

### 安装 apr

	./configure --prefix=/opt/software/apr

### 安装 apr-util
	
	./config --prefix=/opt/software/apr-util --with-apr=/opt/software/apr

### 安装httpd

	./configure --prefix=/opt/software/httpd --with-mpm=worker --enable-cache --enable-disk-cache --enable-mem-cache --enable-file-cache --enable-nonportable-atomics --enable-mods-shared=most --enable-so --enable-rewrite --enable-ssl --with-apr=/opt/software/apr/ --with-apr-util=/opt/software/apr-util/

###  加入系统服务
	
	cp /opt/software/httpd/bin/apachectl /etc/init.d/httpd

### 修改 httpd.conf

	ServerName localhost