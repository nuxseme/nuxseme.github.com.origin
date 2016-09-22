---
title: mongodb综合
categories:
  - MongoDB
date: 2016年9月22日19:33:43
---

### MongoDB 综合

#### mongodb 安装

> mongodb 下载地址[http://www.mongodb.org/downloads](http://www.mongodb.org/downloads)  

	wget url/mongodb.tgz    # 下载
	tar -zxvf mongodb.tgz   # 解压
	mv  mongodb/ /path/mongodb   # 将解压包拷贝到指定目录

> 将mongodb bin目录加入path mongodb/bin下常用二进制文件 mongo  mongod 可以自建软连接mongodb源码解压没有mongodb.conf文件,可以自己手动建立，建议mongodb.conf文件不要放在mongodb目录(升级可能会被覆盖)可以放到自定义的配置目录下,手动建立logpath dbpath 目录,可能因为没有这两个目录导致无法启动   

	$mongod -f|--config /path/mongodb.conf

<!--more-->
### mongodb.conf   
 > mongodb 查看配置可以ps aux|grep mongodb 会显示mongodb启动的命令  
   或者进入mongodb.conf查看

	#configuration Options for MongoDB 
	# 
	# For More Information, Consider: 
	# - Configuration Parameters: http://www.mongodb.org/display/DOCS/Command+Line+Parameters 
	# - File Based Configuration: http://www.mongodb.org/display/DOCS/File+Based+Configuration 
	dbpath = /var/mongodb/db1/ 
	logpath = /var/mongodb/log1/mongodb.log 
	logappend = true 
	#绑定监听地址 
	#需要远程连接 修改为0.0.0.0 
	#默认为127.0.0.1 
	bind_ip = 0.0.0.0 
	port = 27017 
	fork = true 
	#是否需要认证
	#auth = true 
	noauth = true 
	directoryperdb = true 
	journal = true
	#存储引擎 mmapv1|WiredTiger
	#mongoVUE  nosql manager for mongodb 无法识别WirdTiger引擎
	#不同的引擎生成的数据格式不一样 可在dbpath下查看
	#生成以wt结尾的文件就表明使用的是WiredTiger引擎
	storageEngine = mmapv1

### mongo  
> mongo是mongodb自带的shell客户端

	$./mongo db #可选择数据集合名称 通常进去会链接到test数据库
	$help #进入之后 help查看指令


### mongo-php-driver 与 mongodb-php-driver

> mongodb-php-driver 是 mongo-php-driver的升级版本使用方式跟mongo-php-driver有别差  
  mongo-php-driver   MongoClient
  mongodb-php-driver MongoDB\Driver\Manager...

