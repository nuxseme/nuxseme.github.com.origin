---
title: nginx-应用场景
date: 2019-07-03 14:23:28
categories:
---


#### 静态资源web服务器

* 静态资源类型

> 非服务器动态运行生成的文件，换句话说，就是可以直接在服务器上找到对应文件的请求

* 静态资源服务场景-CDN


* nginx 静态资源配置

		配置域：http、server、location 
		#文件高速读取
		http {  
		     sendfile   on;
		}
		#在 sendfile 开启的情况下，开启 tcp_nopush 提高网络包传输效率
		#tcp_nopush 将文件一次性一起传输给客户端，就好像你有十个包裹，快递员一次送一个，来回十趟，开启后，快递员讲等待你十个包裹都派件，一趟一起送给你
		http {  
		     sendfile   on;
		     tcp_nopush on;
		}
		#tcp_nodelay 开启实时传输，传输方式与 tcp_nopush 相反，追求实时性，但是它只有在长连接下才生效
		http {  
		     sendfile   on;
		     tcp_nopush on;
		     tcp_nodelay on;
		}
		
		#将访问的文件压缩传输 （减少文件资源大小，提高传输速度）
		#当访问内容以gif或jpg结尾的资源时
		location ~ .*\.(gif|jpg)$ {  
		    gzip on; #开启
		    gzip_http_version 1.1; #服务器传输版本
		    gzip_comp_level 2; #压缩比，越高压缩越多，压缩越高可能会消耗服务器性能
		    gzip_types   text/plain application/javascript application/x-javascript text/javascript text/css application/xml application/xml+rss image/jpeg image/gif image/png;     #压缩文件类型
		    root /opt/app/code;     #对应目录（去该目录下寻找对应文件）
		}
		
		#直接访问已压缩文件
		#当访问路径以download开头时，如www.baidu.com/download/test.img
		#去/opt/app/code目录下寻找test.img.gz文件，返回到前端时已是可以浏览的img文件
		location ~ load^/download {  
		    gzip_static on #开启;
		    tcp_nopush on;
		    root /opt/app/code;
		}
	
	
	
#### 浏览器缓存

	location ~ .*\.(html|htm)$ {  
    expires 12h;    #缓存12小时
	}
	
> 服务器响应静态文件时，请求头信息会带上 etag 和 last_modified_since 2个标签值，浏览器下次去请求时，头信息发送这两个标签，服务器检测文件有没有发生变化，如无,直接头信息返 etag 和last_modified_since，状态码为 304 ，浏览器知道内容无改变,于是直接调用本地缓存，这个过程也请求了服务，但是传着的内容极少



#### 跨站访问
	
	ocation ~ .*\.(html|htm)$ {  
     add_header Access-Control-Allow-Origin *;
     add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,OPTIONS;
     #Access-Control-Allow-Credentials true #允许cookie跨域
	}

> 在响应中指定 Access-Control-Allow-Credentials 为 true 时，Access-Control-Allow-Origin 不能指定为 *，需要指定到具体域名


#### 防盗链

> 防止服务器内的静态资源被其他网站所套用
此处介绍的 nginx 防盗链为基础方式，其它更加深入的方式将在之后的文章介绍

首先，需要理解一个nginx变量

	$http_referer #表示当前请求上一次页面访问的地址，换句话说，访问 www.baidu.com 主页，这是第一次访问，所以 $http_referer 为空，但是 访问此页面的时候还需要获取一张首页图片，再请求这张图片的时候 $http_referer 就为 www.baidu.com 
	
	location ~ .*\.(jpg|gif)$ {  
    #valid_referers 表示我们允许哪些 $http_referer 来访问
    #none 表示没有带 $http_referer，如第一次访问时 $http_referer 为空
    #blocked 表示 $http_referer 不是标准的地址，非正常域名等
    #只允许此ip
    valid_referers none blocked 127.xxx.xxx.xx
    if ($invalid_referer) {     #不满足情况下变量值为1
        return 403;
    }
	}


#### HTTP代理服务

正向代理代理的对象是客户端  
反向代理代理的对象是服务端


	语法：proxy_pass URL
	默认：——
	位置：loaction


	#代理端口
	#场景：服务器80端口开放，8080端口对外关闭，客户端需要访问到8080
	#在nginx中配置proxy_pass代理转发时，如果在proxy_pass后面的url加/，表示绝对根路径；如果没有/，表示相对路径，把匹配的路径部分也给代理走
	server {
    listen 80;
    location / {
        proxy_pass http://127.0.0.1:8080/;
        proxy_redirect default;
        
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr; #获取客户端真实IP
        
        proxy_connect_timeout 30; #超时时间
        proxy_send_timeout 60;
        proxy_read_timeout 60;
        
        proxy_buffer_size 32k;
        proxy_buffering on; #开启缓冲区,减少磁盘io
        proxy_buffers 4 128k;
        proxy_busy_buffers_size 256k;
        proxy_max_temp_file_size 256k; #当超过内存允许储蓄大小，存到文件
    }
	}