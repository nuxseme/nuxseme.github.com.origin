---
title: 找不到php.ini
categories:
  - PHP
date: 2016-06-28 23:02:58
---
### 问题描述
在指定的文件目录下已经存在php.ini 重启了php-fpm依然无法加载php.ini

```
$ php -i|head
phpinfo()
PHP Version => 7.0.8
System => Linux VM_237_61_centos 3.10.0-327.el7.x86_64 #1 SMP Thu Nov 19 22:10:57 UTC 2015 x86_64
Build Date => Jun 28 2016 22:33:02
Configure Command =>  './configure'  '--prefix=/opt/software/php7' '--with-curl' '--with-freetype-dir' '--with-gd' '--with-gettext' '--with-iconv-dir' '--with-kerberos' '--with-libdir=lib64' '--with-libxml-dir' '--with-mysqli' '--with-openssl' '--with-pcre-regex' '--with-pdo-mysql' '--with-pdo-sqlite' '--with-pear' '--with-png-dir' '--with-xmlrpc' '--with-xsl' '--with-zlib' '--enable-fpm' '--enable-bcmath' '--enable-libxml' '--enable-inline-optimization' '--enable-gd-native-ttf' '--enable-mbregex' '--enable-mbstring' '--enable-opcache' '--enable-pcntl' '--enable-shmop' '--enable-soap' '--enable-sockets' '--enable-sysvsem' '--enable-xml' '--enable-zip'
Server API => Command Line Interface
Virtual Directory Support => disabled
Configuration File (php.ini) Path => /opt/software/php7/lib
Loaded Configuration File => (none)
```

```
[ /opt/software/php7/lib ]$ ll
total 84
drwxr-xr-x  3 root root  4096 Jun 28 22:38 .
drwxr-xr-x  9 root root  4096 Jun 28 22:33 ..
drwxr-xr-x 15 root root  4096 Jun 28 22:33 php
-rw-r--r--  1 root root 68935 Jun 28 22:38 php.ini
```
### 跟踪命令

```
strace  php -i 2> /tmp/strace.php.ini.log
```

```
$ cat /tmp/strace.php.ini.log |grep php.ini
open("/opt/software/php7/bin/php.ini", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/opt/software/php/lib/php.ini", O_RDONLY) = -1 ENOENT (No such file or directory)
write(1, "Configuration File (php.ini) Pat"..., 33) = 33
```
问题找到了 php加载php.ini 会在--prefix/bin或者--prefix同级下的php/lib加载！  
### 解决方案
>1 复制文件到对应的目录下 不需要重新编译，立即见效,没有仔细看过为啥会去php/lib目录下找对应的php.ini，所以新建目录php/lib不是很确定会不会对后面的扩展有影响
>2 重新编译 --prefix=/opt/php (已验证)






