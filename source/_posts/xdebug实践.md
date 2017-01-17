---
title: xdebug实践
categories: debug
date: 2016-11-10 19:42:24
---


### 服务端安装xdebug扩展

    [Xdebug]
    zend_extension = xdebug.so
    xdebug.remote_port = 9000 #远端监听的端口
    xdebug.remote_enable = true
    xdebug.remote_handler=dbgp
    xdebug.remote_mode=req
    xdebug.remote_connect_back=1 #结果返回给请求的链接
    xdebug.idekey="PHPSTORM"; # idekey
    xdebug.profiler_enable = 1
    
 <!--more-->  
### IDE设置

>phpstorm
 preferences->language & frameworks->PHP->Debug   设置xdebug port  9000  （对应上即可）  
 preferences->language & frameworks->Debug->DBGp  IDE KEY 服务端访问地址，监听的端口
 preferences->language&frameworks->Server 新建,名称=>debug server name ,host=>服务器的网址，port=>端口，勾选文件映射，文件路径要正确(debug 变量值才会显示在IDE代码上面)
 
### debug配置项

>run->edit config-> 新建配置项  
 http request 设置完全之后 需要设置断点 启动之后(电话按钮点亮变绿)就可以看到结果
 remote debug 设置断点  启动监听  在浏览器输入访问的网址  
 web application 设置断点  
 
### 建议配置项

>php.ini->max_execution_time=7200;时间过短会出现debug过程中报502 Time-out
