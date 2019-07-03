---
title: nginx-基础篇
categories: nginx
date: 2019-07-02 16:42:48
---


#### 优势

* IO多路复用epoll（IO复用） 

> 事件发生主动通知，非轮询

* 轻量级  

> 1 功能模块少，其他模块以插件方式添加  
> 2 代码模块化，适合再次开发

* cpu亲和

>  把CPU核心和Nginx工作进程绑定，把每个worker进程固定在一个CPU上执行，减少切换CPU的cache miss，从而提高性能


#### 基本配置

	user                    #设置nginx服务的系统使用用户
	worker_processes        #工作进程数 一般情况与CPU核数保持一致
	error_log               #nginx的错误日志
	pid                     #nginx启动时的pid
	
	events {
	    worker_connections    #每个进程允许最大连接数
	    use                   #nginx使用的内核模型
	}
	
	
	
	
	http {
    ... ...        #后面再详细介绍 http 配置项目
    
	    server {
	        listen 80                          #监听端口;
	        server_name localhost              #地址
	        
	        #sever可以配置多个location
	        location / {                       #访问首页路径
	            root /xxx/xxx/index.html       #默认目录
	            index index.html index.htm     #默认文件 
	        }        
	        
	        error_page  500 504   /50x.html    #当出现以上状态码时从新定义到50x.html        
	        location = /50x.html {             #当访问50x.html时
	            root /xxx/xxx/html             #50x.html 页面所在位置
	        }        
	    }
    
	    server {
	        ... ... 
	    } 
   	}
   	
   	#可以配置多个server



	http {
	    sendfile  on                  #高效传输文件的模式 一定要开启
	    keepalive_timeout   65        #客户端服务端请求超时时间
	    log_format  main   XXX        #定义日志格式 代号为main
	    access_log  /usr/local/access.log  main     #日志保存地址 格式代码 main
	    
	    #'$request|$status|$request_time|$upstream_response_time';
	    # request_time 指的就是从接受用户请求的第一个字节到发送完响应数据的时间，即包括接收请求数据时间、程序响应时间、输出响应数据时间  
	    # upstream_response_time  是指从Nginx向后端（php-cgi)建立连接开始到接受完数据然后关闭连接为止的时间
	}
