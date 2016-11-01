---
title: Centos下安装php7总结
categories:
  - php
date: 2016-06-29 23:06:58
---
### 简介
安装几次php7了，每次都要到网上找对应的依赖包，花很长时间，这里作下记录（记录可能有缺漏）
### 下载php7  
以实际安装版本为准  

```
wget http://cn2.php.net/distributions/php-7.0.8.tar.gz
```
### 安装libmcypt

```
wget ftp://mcrypt.hellug.gr/pub/crypto/mcrypt/libmcrypt/libmcrypt-2.5.7.tar.gz
tar -zxvf libmcrypt-2.5.7.tar.gz
./configure
make && make install
```
<!--more-->
### 安装依赖

```
yum -y install libxml2 libxml2-devel libcurl.x86_64 libcurl-devel.x86_64 libjpeg.x86_64 libpng.x86_64 freetype.x86_64  libpng-devel.x86_64 freetype-devel.x86_64 openssl.x86_64 openssl-devel.x86_64 bzip2-devel.x86_64 libjpeg-devel libpng libXpm-devel libxslt libxslt-devel
```
### configure  
注意--prefix（这里设为/opt/software/php7 到后面无法加载php.ini 见[找不到php.ini](/PHP/找不到php-ini/)）

```
./configure    --prefix=/opt/software/php   --with-curl   --with-freetype-dir  --with-gd   --with-gettext   --with-iconv-dir   --with-kerberos   --with-libdir=lib64   --with-libxml-dir   --with-mysqli   --with-openssl  --with-pcre-regex   --with-pdo-mysql   --with-pdo-sqlite   --with-pear   --with-png-dir   --with-xmlrpc   --with-xsl   --with-zlib   --enable-fpm  --enable-bcmath   --enable-libxml   --enable-inline-optimization   --enable-gd-native-ttf   --enable-mbregex   --enable-mbstring   --enable-opcache   --enable-pcntl   --enable-shmop   --enable-soap  --enable-sockets   --enable-sysvsem   --enable-xml   --enable-zip
make && make install
```
### 配置修改

```
cp /opt/download/php-7.0.8/php.ini-development /opt/software/php/lib/php.ini
cp /opt/download/php-7.0.8/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm  //serveice php-fpm start
cp/php-fpm.conf.default php-fpm.conf
cp www.conf.default www.conf
```
### 附php常用指令 
 
```
php --ri extension_name #查看扩展详情
PHP -i # 显示phpinfo() 配合grep 可以查看对应的参数
php -r "php"; # 执行简单的php语句
php -v # 显示php版本
php -m # 查看扩展
PHP --ini # 查看php.ini路径
``` 




