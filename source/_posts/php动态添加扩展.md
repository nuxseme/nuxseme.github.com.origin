---
title: php动态添加扩展
date: 2016-09-20 22:27:43
categories:
  - php
---


### php动态添加扩展

> 安装完成php之后，会遇到想要添加扩展，这里可以动态添加  
> 下载安装的扩展源码 假设安装mongo-php-driver
	
	$tar -zxvf mongo-php-driver.tar.gz
	$cd mongo-php-driver
	$phpize
	$./configure --with-php-config=/path/php5/bin/php-config
	$make && make install

> 编译完成之后会在指定的php扩展目录下会包含mongo.so
> 修改php.ini文件 echo "extension=mongo.so">>php.ini

	$php --ri mongo #查看mongo扩展



