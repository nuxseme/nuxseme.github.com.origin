---
title: nginx-基础篇  
date: 2019-07-02 16:42:48  
categories: nginx
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



	http {
	    sendfile  on                  #高效传输文件的模式 一定要开启
	    keepalive_timeout   65        #客户端服务端请求超时时间
	    log_format  main   XXX        #定义日志格式 代号为main
	    access_log  /usr/local/access.log  main     #日志保存地址 格式代码 main
	}
