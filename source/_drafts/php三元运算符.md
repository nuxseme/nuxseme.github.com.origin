---
title: php三元运算符
date: 2016-12-19 17:41:47
categories: php
---

	//变量已定义
	$foo = 0;
	$ham = 1;
	echo $foo ? true : false;//true
	
	echo $foo ? $foo : $ham;
	//=>
	echo $foo ?: $ham
	
	//变量未定义
	echo isset($foo) ? $foo : $ham;//$ham
	//=>新特性
	echo $foo ?? $ham;
