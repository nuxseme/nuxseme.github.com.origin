---
title: phing
categories: php
date: 2017-03-07 16:16:32
---


### phing php自动部署工具 

> 官网[https://www.phing.info](https://www.phing.info)  
> git[https://github.com/phingofficial/phing](https://github.com/phingofficial/phing)  
> Phar[phing-latest.phar](https://www.phing.info/get/phing-latest.phar)(推荐)  
> composer require "phing/phing:2.*"  

### 安装

	$wget http://www.phing.info/get/phing-2.16.0.phar
	$ll
	-rwxr-xr-x. 1 root root 3473784 Dec 23 04:40 phing-2.16.0.phar
	$ln -s ${pwd}/phing-2.16.9.phar ~/bin/phing  //配置到全局 自定义
	$phing -v
	Phing 2.16.0

<!--more-->
### 使用
	
	<?xml version="1.0" encoding="UTF-8"?>
	<project name="phing" default="dist">//首先开始执行的target
	<property file="deploy.properties"/>
	    <target name="dist">
	        <echo msg="${context}" />
		<copy todir="${installdir}" overwrite="true">
	          <fileset dir="../phing" />
	        </copy>
		
	    </target>
	</project>

	//project default属性默认开始执行的target
	<target name="foo" depends="get">
	//target 属性depends 表明当前的target需要依赖get target先执行完毕

	<target name="publish">
	  <scp username="***" password="***" host="***" todir="dir">	
	    <fileset dir=".">	
	    </fileset>
	
	  </scp>
	
	</target>
	//支持远程部署  将测试完毕的文件部署到生产
	<phpunit codecoverage="true" failureproperty="" >
	</phpunit>
	//支持单元测试  代码覆盖率