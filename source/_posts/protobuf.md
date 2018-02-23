---
title: protobuf
categories: transfer protocol
date: 2018-02-05 17:08:20
---


### 简介
> 功能可以理解为xml或者json，将数据格式化用于存储和传输


### 优缺点

> 优点：相比于json额外传输数据类型，xml额外传输格式定义的内容，pb能减少网络传输。在数据装包和解包的性能上也非常优秀  
> 缺点：二进制数据无法直接可视化，需要定义pb协议文件，cs端必须保持一致


### protoc安装

	$protobuf-php-3.5.1.zip
	$make && make install
	$ protoc --version
	libprotoc 3.5.1
	$protoc --php_out=$PWD *.proto

### 例子

	syntax = "proto3";
	package proto;
	message User {
     string name = 1;
     int32 id = 2;
	}

	$user =  new \Proto\User();
	$user->setId(1);
	$user->setName('hello proto');

	$serializeUser = $user->serializeToString();

	//print_r($user);
 	var_dump($serializeUser);

	$user = new \Proto\User();
	$user->mergeFromString($serializeUser);
	echo $user->getId();
	echo $user->getName();