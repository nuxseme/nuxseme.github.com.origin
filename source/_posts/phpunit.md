---
title: phpunit
categories: php
date: 2017-12-08 11:21:02
---


# 安装

* composer 安装  
> composer require --dev phpunit/phpunit ^x.y composer安装需要注意，将vender/bin/phpunit加入到$path变量中,这样安装的phpunit是一个脚本文件，在使用可能会缺少其他的依赖比如PHP_Invoker，这是因为composer方式安装没有包含这个依赖包,需要手动再次composer require --dev phpunit/php-invoker。在项目开发中可以添加在外部扩展，IDE可以有提示

* phar安装(建议)
> phpunit 以及相关的依赖已经在打包在一个可执行文件里，下载对应的版本

<!--more-->

# 基境

* setUp
> 每个测试方法在跑之前都会先执行的方法，常用场景，初始化数据

* tearDown
> 测试方法运行结束后，不管是成功还是失败，都会调用tearDown

* setUpBeforeClass 
> 在测试类跑之前执行一次，常用于设置，数据库连接，设置登录态  

* tearDownAfterClass
> 在测试类结束之后执行一次，用于释放数据库，删除测试数据等等

# 桩
> 待续

# phpunit.xml 
> 测试编排

	<phpunit bootstrap="bootstrap.php"
        colors="true"
        convertErrorsToExceptions="true"
        convertNoticesToExceptions="true"
        convertWarningsToExceptions="true"
        stopOnFailure="true">
    <testsuites>
        <testsuite name="dir">
           <directory>path</directory>
        </testsuite>
    </testsuites>
	</phpunit>
# bootstrap

> 执行测试文件之前加载的文件
> 加载配置文件
> 加载自动加载文件
> ...
> 通常修改自index文件
