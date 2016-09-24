---
title: vim
categories:
  - null
date: 2016-09-24 14:50:25
---

	set nu|number|nonumber|nonu 是否开齐行号
	set ff=unxi 设置文件格式
	set cindent 缩进
	syntax on|off 高亮
	nohl no hight light
	/term  + n (next)向下搜索
	？向上
	shift + G  //文件尾
	gg 	//文件头
<!--more-->
> vim初始化配置，在/etc/vimrc 尾部添加配置

	#开启|关闭行号
	set nu |nonu
	#自动换行
	set wrap 
	#unix文件格式
	set ff=unix
	#table键
	set ts=4
	#语法高亮
	syntax on