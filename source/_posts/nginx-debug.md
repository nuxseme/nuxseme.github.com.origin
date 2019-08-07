---
title: nginx-debug
date: 2019-08-07 09:49:33
categories: nginx
---

#### 概述

> nginx debug配置，比较好的方案是在在测试环境开启--with-debug模式，查看error日志  
> nginx echo模块测试了下，不是特别方便使用


#### --whit-ddebug

	./configure --with-dedebug
	error_log  nginx.error.log debug;

>如果发生错误可以在error日志查看，变量的值，执行的流程



#### --add-moudle=/echo-nginx-module-0.61

> 可以在localtion 里面echo字符串和变量，输出到浏览器