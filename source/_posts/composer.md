---
title: composer
categories:
  - composer
date: 2016-11-1 11:22:57
---

### composer 

>类似npm yum apt-get Homebrew 的php包管理器。在构建项目时，常常需要引进轮子，为了避免不断的include,不断的升级，composer管理器会根据制定的composer.json引进需要的php包，并自动生成自动加载代码，只要require autoload.php就可以对应命名空间包含我们需要的类文件。

### composer command

	composer config #查看coposer配置
	composer init  #初始化一个composer.json 交互式填写
	composer install #根据composer.json 构建
	composer remove package # 移除
	composer require "package:version" #引入
	
<!--more-->
### composer origin 
  	
  	
	方法一：
	composer config repo.packagist composer https://packagist.phpcomposer.com #全局
	方法二：
	//composer.json
	"repositories": {
    "packagist": {
        "type": "composer",
        "url": "https://packagist.phpcomposer.com"
    	}
	}
	
	
### 制作自己的包
>将自己的库包放到github上，项目中通过composer 安装

	
	{
    "name": "nuxseme/composer.origin",#引入的包名
    "type": "library",
    "description": "composer template",
    "keywords": ["composer template"],
    "homepage": "http://nuxseme.com",
    "license": "MIT",
    "authors": [
        {"name": "nuxseme", "email": "nuxseme@gmail.com"}
    ],
    "require": {
        "php": ">=7"
    },
    "autoload": {
    		#按照命名空间对应
        "psr-0": { "Nuxseme\\APP\\": "lib/" }
    }
    }
    #需要在github仓库发布初始的release
    
    
    {
    "name": "hyd/composer-test",
    "type": "project",
    "license": "MIT",
    "authors": [
        {
            "name": "hyd",
            "email": "406671554@qq.com"
        }
    ],
    "repositories": [
        {
            "type": "vcs",
            "url": "git@github.com:nuxseme/composer.origin.git" #包地址
        },
        {
            "packagist": false #禁止显示packagelist
        }
       #注意： 顺序是非常重要的，当 Composer 查找资源包时，它会按照顺序进行。默认情况下 Packagist 是最后加入的，因此自定义设置将可以覆盖 Packagist 上的包。
 
    ],
    "require": {
    		#对应包名称 
        "nuxseme/composer.origin": ">=1.0.0"
    	}
	}
