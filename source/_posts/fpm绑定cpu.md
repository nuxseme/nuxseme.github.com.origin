---
title: fpm绑定cpu
date: 2019-08-07 11:44:07
categories: php
---

> fpm进程设置cpu亲和性，前提是php-fpm配置成static模式  （static静态 dynamic动态  ondemand按需）
> 后续需要测试下是否会发生1核有难9核围观的场景??

	#!/bin/bash
	cat /usr/local/php7/var/run/php-fpm.pid | xargs kill -USR2
	sleep 3
	CPUs=$(grep -c processor /proc/cpuinfo)
	PIDs=$(ps -ef | grep "php-fpm" | awk '{print $2}')
	
	let i=0
	for PID in $PIDs; do
	   CPU=$(echo "$i % $CPUs" | bc)
	   let i++
	
	   taskset -pc $CPU $PID
	done