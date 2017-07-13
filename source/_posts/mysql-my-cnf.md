---
title: mysql-my-cnf
categories: mysql
date: 2017-07-13 18:48:15
---


### 查找my.cnf

	$mysql --help | grep 'Default options' -A 1
	Default options are read from the following files in the given order:
	/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf
	
>按顺序查找my.cnf

### my.cnf

	# Default Homebrew MySQL server config
	[mysqld]
	# Only allow connections from localhost
	bind-address = 127.0.0.1 //绑定127.0.0.1之后
	
	mysql -hlocalhost -u$user -p$password会报错
	ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
