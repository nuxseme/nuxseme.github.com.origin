---
title: xdebug原理
categories: debug
date: 2016-11-10 19:41:54
---


![xdebug示意图](/img/xdebug.gif)
>xdebug 是php下的一个扩展，首先需要编译安装xdebug.xdebug分为server和client。server指的就是运行环境的xdebug扩展，client这里可以看成是IDE。在本地开发，代码同步到运行环境，假定请求服务器的地址，请求到达nginx,nginx转给php-fpm.php拿到请求，一看XDEBUG_SESSION_START，告诉xdebug,开发者要调式代码。xdebug接到命令，看看自己的配置，是否指定了IDE KEY 是否指定了IDE的ip。xdebug用自己的协议发送远端，询问是不是需要代码调试。php运行一行指令，就询问xdebug，接下来是否需要调试，xdebug跑去开发者哪里看了一下，设置了断点，就将运行环境数据发给client展示给开发者。

<!--more-->

>php.ini配置的xdebug配置项，host指的是IDE的ip， 端口是指IDE监听的端口，所以两端的端口需要匹配。IDE server的ip指的是服务器的ip.  多人调试，设置remote_connect_back=1 不需要设置remote_host,这样xdebug会将调试结果发送给对应的请求者