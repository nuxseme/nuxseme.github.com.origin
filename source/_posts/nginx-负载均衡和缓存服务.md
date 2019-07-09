---
title: nginx-负载均衡和缓存服务
categories: nginx
date: 2019-07-08 15:54:39
---


#### 负载均衡
 
负载均衡的实现方法就是我们上章介绍的反向代理 。将客户的请求通过 nginx 分发（反向代理）到一组多台不同的服务器上

这一组服务器我们称为 服务池（upstream server），池内的每一个服务器称为一个 单元，服务池内将对每一个单元进行请求轮训，实现负载均衡

	#配置
	语法：upstream name ...
	默认：——
	位置：http
	
 		
	upstream #自定义组名 {
    server x1.baidu.com;    #可以是域名
    server x2.baidu.com;    
    #server x3.baidu.com    
                            #down         不参与负载均衡
                            #weight=5;    权重，越高分配越多  
                            #backup;      预留的备份服务器
                            #max_fails    允许失败的次数
                            #fail_timeout 超过失败次数后，服务暂停时间
                            #max_coons    限制最大的接受的连接数
                            #根据服务器性能不同，配置适合的参数
    
    #server 106.xx.xx.xxx;        可以是ip
    #server 106.xx.xx.xxx:8080;   可以带端口号
    #server unix:/tmp/xxx;        支出socket方式
	}
	
	
假设我们有三台服务器，并且假设它们的IP地址，前端负载均衡服务器A（127.0.0.1），后台服务器B（127.0.0.2），后台服务器C（127.0.0.3）

新建文件 proxy.conf，内容如下，上一章介绍的反向代理配置



	proxy_redirect default;
	proxy_set_header Host $http_host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_connect_timeout 30;
	proxy_send_timeout 60;
	proxy_read_timeout 60;
	proxy_buffer_size 32k;
	proxy_buffering on;
	proxy_buffers 4 128k;
	proxy_busy_buffers_size 256k;
	proxy_max_temp_file_size 256k;
	
	
	#服务器A的配置
	http {
	    ...
	    upstream xxx {
	        server 127.0.0.2;
	        server 127.0.0.3;
	    }
	    server {
	        liseten 80;
	        server_name localhost;
	        location / {
	            proxy_pass http://xxx     #upstream 对应自定义名称
	            include proxy.conf;  
	        }
	    }
	}
	
	#服务器B、服务器C的配置
	server {
	    liseten 80;
	    server_name localhost;
	    location / {
	         index  index.html
	    }
	}
	
	
调度算法

* 轮训：按时间顺序逐一分配到不同的后端服务器  
* 加权轮训：weight值越大，分配到的几率越高
* ip_hash：每个请求按访问IP的hash结果分配，这样来自同一个IP固定访问一个后端服务器
* least_conn：最少链接数，哪个机器连接数少就分发给谁
* url_hash：按照访问的URL的hash结果来分配请求，每一个URL定向到同一个后端服务器
* hash关键数值：hash自定义key

ip_hash 配置

	 upstream xxx {
	        ip_hash;
	        server 127.0.0.2;
	        server 127.0.0.3;
	  }
	  
ip_hash存在缺陷，当前端服务器再多一层时，将获取不到用户的正确IP，获取的将是前一个前端服务器的IP，因此 nginx1.7.2版本推出了 url_hash

url_hash 配置

	 upstream xxx {
        hash $request_uri;
        server 127.0.0.2;
        server 127.0.0.3;
 	 }


#### 缓存服务

##### 缓存类型

* 服务端缓存：缓存存储在后端服务器，如redis,memcache
* 代理缓存：缓存存储在代理服务器或者中间件上，它的内容是从后端服务器获取的，但是保存在自己本地
* 客户端缓存：缓存在浏览器内的

##### nginx 代理缓存
客户端请求nginx，nginx查看本地是否有缓存数据，若有直接返回给客户端，若没有再去后端服务器请求

	http {          
    proxy_cache_path    /var/www/cache #缓存地址
                        levels=1:2 #目录分级
                        keys_zone=test_cache:10m #开启的keys空间名字:空间大小(1m可以存放8000个key)
                        max_size=10g #目录最大大小(超过时，不常用的将被删除)
                        inactive=60m #60分钟内没有被访问的缓存将清理
                        use_temp_path=pff; #是否开启存放临时文件目录，关闭默认存储在缓存地址 
                                         
    server {
        ...
        location / {
            proxy_cache test_cache;    #开启缓存对应的名称，在keys_zone命名好
            proxy_cache_valid 200 304 12h;    #状态码为200 304的缓存12小时
            proxy_cache_valid any 10m;    #其他状态缓存10小时
            proxy_cache_key $host$uri$is_args$args;    #设置key值
            add_header Nginx-Cache "$upstream_cache_status";
        }     
    }                    
	}
	
当有个特定请求我们不需要缓存的时候，在上面配置的内容中加入以下配置

	server {
	    ...
	    if ($request_uri ~ ^/(login|register) ) {    #当请求地址有login或register时
	        set $nocache = 1;    #设置一个自定义变量为true
	    }  
	    location / {
	        proxy_no_cache $nocache $arg_nocache $arg_comment;
	        proxy_no_cache $http_pragma $http_authoriztion;
	    }
	}
	
##### 分片请求

早期版本 nginx 对大文件的分片请求不支持缓存，1.9版本后slice模块实现了这个功能
前端发起请求，nginx去获取这个请求文件的大小，若超过我们的定义slice的大小，会进行切片，分割成多个小的请求去请求后端，到前端就成为一个一个独立的缓存文件

优势：每个子请求收到的数据都会形成独立文件，一个请求中断了，其他请求不受影响，原本情况请求中断，再次请求文件将从头开始，而开启分片请求，就接下去获取未请求的小文件

劣势：当文件很大或者slice很小时，可能会导致文件描述符耗尽等情况

	语法：   slice size;    #当大文件请求时，设置size为每个小文件的大小
	默认：   slice 0;
	位置：   http/server/location