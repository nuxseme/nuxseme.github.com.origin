---
title: cxsv源码分析系列（二）扩展安装
date: 2016-06-26 00:59:41
categories: 
- cxsv
---
### Notic
以下安装仅仅作为示例，具体安装版本以实际情况为准，./configure的配置参数参看./configure --help，配置文件路径以实际为准。

### 安装swoole-src-swoole-1.8.4-stable
```
tar zxvf swoole-src-swoole-1.8.4-stable.tar.gz
cd swoole-src-swoole-1.8.4-stable
phpize
./configure --enable-sockets --enable-async-mysql --enable-openssl
make && make install
```
<!--more-->

### 安装mongodb-1.1.1-php7
```
yum install cyrus-sasl-devel
tar zxvf mongodb-1.1.1-php7.tgz
cd mongodb-1.1.1
phpize
./configure
make && make install
```

### 安装libmemcached-1.0.18
```
tar zxvf libmemcached-1.0.18.tar.gz
cd libmemcached-1.0.18
./configure --prefix=/usr/local/libmemcached
make && make install
```

### 安装php-memcached-php7
```
unzip php-memcached-php7.zip
cd php-memcached-php7
phpize
./configure --with-libmemcached-dir=/usr/local/libmemcached --disable-memcached-sasl
make && make install
```

### 安装phpredis-php7
```
unzip phpredis-php7.zip
cd phpredis-php7
phpize
./configure
make && make install
```
### 修改php.ini
	echo extension=swoole.so >> /usr/local/php/lib/php.ini  
	echo extension=redis.so >> /usr/local/php/lib/php.ini
	echo extension=mongo.so >> /usr/local/php/lib/php.ini
	echo extension=memcached.so >> /usr/local/php/lib/php.ini
	
### php指令简介
	$php -v #显示php版本，检验是否安装了php
	$php -m|grep swoole #检测是否安装swoole扩展
	$php -i #显示phpinfo 可以查看php.ini实际引用目录 configure参数
	$php -r "echo phpinfo();" #执行php代码
	$php -r "echo SWOOLE_VERSION;" #显示swoole版本
	$php --ri swoole #显示swoole简要信息
	
	
	
	
	
	
	
	
	
	
	
	