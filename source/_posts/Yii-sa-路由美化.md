---
title: Yii-sa-路由美化
date: 2016-08-30 11:41:00
categories:
  - yii
---



需要将 example.com/index.php?r=controllerName/Action  
改写成 example.com/contollerName/Action

1 修改web服务器配置  
  nginx如下：  
  $uri|$uri/找不到文件尝试将url改写为  
  $uri/index.php?$args 定位到入口文件  

>  
	location / {
		 try_files $uri $uri/ /index.php?$args;
    }


2 修改app web配置

>  
	 'urlManager' => [
            'enablePrettyUrl' => true,//开启url美化
            'showScriptName' => false,//隐藏index.php脚本
            'rules' => [
            ],
        ],

3 访问格式
example.com/controller/action-action  => Controller/actionActionAction  
中间用'-'隔开,控制器的action使用驼峰表示法