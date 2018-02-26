---
title: docker构建移动开发环境
categories: docker
date: 2018-02-18 09:15:58
---



### php-fpm
>先运行fpm拿到fpm的ip，nginx的代理需要用到

	docker run -p 9000:9000 --name  php-fpm -v $PWD/www:/www -d php:7-fpm
	
### 运行nginx
	docker run -p 80:80 --name nginx -v $PWD/www:/www -v $PWD/nginx/conf.d:/etc/nginx/conf.d -v $PWD/nginx/log:/nginx/log  -d nginx
	
	server
	{
            listen 80;
            server_name project.a.com;
    		  gzip off;
            error_log /nginx/log/project.a.error.log;
            access_log /nginx/log/project.a.access.log;
            index index.php;
            root /www/project-a;

            location / {
                    try_files $uri $uri/ /index.php?$args;
            }

            location ~ \.php$ {
                    include fastcgi_params;
                    fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
                    fastcgi_pass   172.17.0.2:9000;
                    fastcgi_buffer_size 4k;
                    fastcgi_buffers 8 4k;
                    try_files $uri =404;
            }
	}

> 注意nginx配置文件的fastcgi_pass ip地址修改为php容器的地址

### 配置本地hosts
>127.0.0.1 hostname

### 大致流程

> 1 配置本地host，请求转发到127.0.0.1:80 假定nginx监听的80  
> 2 nginx 接受到请求，根据server_host 和 路由规则将请求转发到php-fpm处理（fastcgi_pass 已经映射到了php-fpm容器的ip:port）  
> 3 php-fpm 监听9000 根据请求到www目录下找到index.php统一入口处理  


### ...

>这里将php-fpm 和 nginx 分别创建了容器，可以设定多个fpm来处理请求  
>解决了开发测试环境的固化（非云端解决方案） 
>环境固化，每次创建一个全新的体验环境  
>容器的编排和管理... 待续
