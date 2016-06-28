---
title: cxsv源码分析系列(一)框架简介
date: 2016-06-25 22:49:20
categories: 
- cxsv源码分析
---
### 概述
cxsv框架跟大多的mvc框架有许多共同点,模块化,库类,单入口.解析动态网页的流程也大致相同[url请求->url解析->Action+Function->渲染->输出]除此之外，cxsv废弃了nginx|apache 采用php扩展[swoole](http://swoole.com)作为http服务器，利用了许多swoole特性，定时器，钩子，任务投递，多进程等特性。该框架配合mongodb,redis,ngnix可以很方便的实现挂件系统，消息队列,日志系统等诸多功能.框架还在进一步完善中。

<!--more-->
### 应用
cxsv提供了应用级别的框架，框架很轻巧，上手很快，很方便快捷的实现通常的应用开发.

### 目录结构
#### 框架目录
> /etc 	#框架配置目录，存放swoole的配置数据，应用配置可放在此处也可放在别处
> /lib 	#库目录
> ├─App	#应用公共的库文件目录[建议放置到其他目录]
> /mod	#模型
> /plugin	#插件目录

#### 应用目录
> /Act		#控制器
> /Cron		#定时
> /Lib		#应用库文件
> /Mod		#模型
> /Plugin	#插件
> /Task		#任务
> /Tpl		#模板
> /Widget	#挂件
