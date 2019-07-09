---
title: nginx-常见配置
date: 2019-07-08 18:07:36
categories: nginx
---

在此之前，先把配置参数所在位置分为四层

1. conf 全局层
2. http 服务器层
3. server 虚拟主机层
4. location 定位层

#### 全局层

	#有1个工作的子进程，可以自行修改，但太大无益，因为要争夺CPU，一般设置为 CPU数*核数
	worker_processes 1; 
	
	#一般是配置nginx连接的特性，如1个子进程能同时允许多少连接
	Event {
	    
	    #这是指一个子进程最大允许连1024个连接
	    worker_connections  1024;
	}
	
	#这是配置http服务器的主要段
	http { 
	     
	     #这是虚拟主机段
	     Server1 {
	      
	            #定位，把特殊的路径或文件再次定位，如image目录单独处理，如.php单独处理
	            Location {  
	            ...    
	            }
	     }
	
	     Server2 {
	         ...
	     }
	}
	
	
<--more-->
#### HTTP服务器层

##### 网页内容的压缩编码与传输速度优化
	
	请求:
	Accept-Encoding:gzip,deflate,sdch
	响应:
	Content-Encoding:gzip
	Content-Length:36093

> 原理:
浏览器---请求----> 声明可以接受 gzip压缩 或 deflate压缩 或compress 或 sdch压缩。

从http协议的角度看--请求头 声明 acceopt-encoding: gzip deflate sdch (是指压缩算法,其中sdch是google倡导的一种压缩方式,目前支持的服务器尚不多)

服务器-->回应---把内容用gzip方式压缩---->发给浏览器
浏览<-----解码gzip-----接收gzip压缩内容

	gzip    #配置的常用参数
	gzip on|off;    #是否开启gzip
	gzip_buffers 32 4K | 16 8K   #缓冲(压缩在内存中缓冲几块? 每块多大?)
	gzip_comp_level  #[1-9] #推荐6 压缩级别(级别越高,压的越小,越浪费CPU计算资源)
	gzip_disable    #正则匹配UA 什么样的Uri不进行gzip
	gzip_min_length 200     #开始压缩的最小长度(再小就不要压缩了,意义不在)
	gzip_http_version 1.0|1.1   # 开始压缩的http协议版本(可以不设置,目前几乎全是1.1协议)
	gzip_proxied          #设置请求者代理服务器,该如何缓存内容
	gzip_types text/plain  application/xml  #对哪些类型的文件用压缩如txt,xml,html ,css ，若不知道类型名称，可以查看nginx下conf文件夹的mime.types
	gzip_vary on|off    #是否传输gzip压缩标志


注意:
图片/mp3这样的二进制文件,不必压缩因为压缩率比较小，比如100->80字节，而且压缩也是耗费CPU资源的。比较小的文件不必压缩。


##### 缓存设置

对于网站的图片，尤其是新闻站，图片一旦发布，改动的可能是非常小的。我们希望 能否在用户访问一次后，图片缓存在用户的浏览器端，且时间比较长的缓存。
可以， 用到nginx的expires设置。


	#在location或if段里
	    expires 30s;
	    expires 30m;
	    expires 2h;
	    expires 30d;
	    
	location ~ image {
	    expires 1d;
	}
	
	
	
另: 304 也是一种很好的缓存手段

原理是: 服务器响应文件内容是，同时响应etag标签(内容的签名，内容一变，他也变)， 和 last_modified_since 2个标签值。浏览器下次去请求时，头信息发送这两个标签， 服务器检测文件有没有发生变化，如无，直接头信息返回 etag，last_modified_since
浏览器知道内容无改变，于是直接调用本地缓存。
这个过程，也请求了服务器，但是传着的内容极少。
对于变化周期较短的，如静态html，js，css,，比较适于用这个方式


#### 虚拟主机层


	#基于域名的虚拟主机
	server {
	    listen 80;  #监听端口
	    server_name a.com; #监听域名
	
	    location / {
	            root /var/www/a.com;   #根目录定位
	            index index.html;
	    }
	}
	
	#基于端口的虚拟主机配置 访问 192.xxx.xx.xxx:8080
	server {
	    listen 8080;    #监听8080端口
	    server_name 192.xxx.xx.xxx;    #服务器IP地址
	
	    location / {
	            root /var/www/html8080;
	            index index.html;
	    }
	}
	
	
##### 日志管理

	#不同的server可以使用不同的log
	#此处定义了日志格式，最好定位在顶层，方便其他server公用
	log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	    #                  '$status $body_bytes_sent "$http_referer" '
	    #                  '"$http_user_agent" "$http_x_forwarded_for"';
	
	$remote_addr                        用户ip
	$remote_user [$time_local]          用户访问时间
	$request                            请求类型 get,post...
	$status                             请求状态 200 304...
	$body_bytes_sent                    请求的内容有多少字节
	$http_referer                       上一个页面来自哪里（从哪里跳转过来）
	$http_user_agent                    用户代理（用了什么浏览器访问）
	
	
	
	#这说明 该server, 它的访问日志的文件,使用的格式main格式.
	access_log  logs/host.access.log  main;
	
写一个sh脚本，每天半夜切分log日志，避免log每天累积造成文件过大

	#!/bin/bash
	base_path='/usr/local/nginx/logs'
	log_path=$(date -d yesterday +"%Y%m")
	day=$(date -d yesterday +"%d")
	mkdir -p $base_path/$log_path
	mv $base_path/access.log $base_path/$log_path/access_$day.log
	#echo $base_path/$log_path/access_$day.log
	kill -USR1 `cat /usr/local/nginx/logs/nginx.pid`
	
	#Crontab 编辑定时任务
	01 00 * * * /xxx/path/b.sh  每天0时1分(建议在02-04点之间,系统负载小)
	
	
#### 定位层
##### location定位
location 有“定位”的意思，根据Uri来进行不同的定位，在虚拟主机的配置中，是必不可少的。location可以把网站的不同部分，定位到不同的处理方式上。

当我们碰到“.php”, 如何调用PHP解释器? --这时就需要location

	#location 的语法
	location [=|~|~*|^~] patt {
	    ...
	}
	#中括号可以不写任何参数,此时称为一般匹配
	#也可以写参数
	#因此,大类型可以分为3种
	#首先看有没有精准匹配,如果有,则停止匹配过程,若没有向下匹配到最符合的location
	location = patt {} #[精准匹配]
	location patt{}  #[一般匹配]
	location ~ patt{} #[正则匹配]
	
匹配顺序实例1

	location / {
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }

	location ~ image {
	           root /var/www/image;
	           index index.html;
	}
	#如果我们访问  http://xx.com/image/logo.png
	#此时, “/” 与”/image/logo.png” 匹配
	#同时,”image”正则 与”image/logo.png”也能匹配,谁发挥作用?
	#正则表达式的成果将会使用.
	#图片真正会访问 /var/www/image/logo.png 
	#注意，若在roo最后加了'/',那么将访问/var/www/image/image/logo.png
	
匹配顺序实例2

	location / {
	             root   /usr/local/nginx/html;
	             index  index.html index.htm;
	         }
	 
	location /foo {
	            root /var/www/html;
	             index index.html;
	}
	#我们访问 http://xxx.com/foo
	#对于uri “/foo”,   两个location的patt,都能匹配他们
	#即 ‘/’能从左前缀匹配 ‘/foo’, ‘/foo’也能左前缀匹配’/foo’,
	#此时, 真正访问 /var/www/html/index.html 
	#原因:’/foo’匹配的更长,因此使用之
	
总结：

先判断精准命中，如果命中，立即返回结果并结束解析过程
判断普通命中，如果有多个命中，记录下最长的命中结果为准（记录但不结束）
继续判断正则表达式的解析结果，按配置里的正则表达式顺序为准，由上往下，一旦匹配成功1个，立即返回结果，并结束解析
分析：

普通命中顺序无所谓，按命中长短来决定
正则命中，按顺序

##### rewrite 重写

	#重写中用到的指令
	if  (条件) {}  #设定条件,再进行重写 
	set #设置变量
	return #返回状态码 
	break #跳出rewrite
	rewrite #重写
	
	#If  语法格式
	If 空格 (条件) {
	    重写模式
	}
	
	#条件语法
	1: “=”来判断相等, 用于字符串比较
	2: “~” 用正则来匹配(此处的正则区分大小写)
	   ~* 不区分大小写的正则
	3: -f -d -e来判断是否为文件,为目录,是否存在.
	
例子

	location / {
	    #当访问ip相等时，返回403
	    if  ($remote_addr = 192.xxx.xx.xx) { 
	        return 403;
	    }
	    
	    #如果是IE浏览器访问 
	    if ($http_user_agent ~ MSIE) {
	        rewrite ^.*$ /ie.htm;
	        break; #若不brea，重定向后又会匹配到IE浏览器，又走到这一步，会循环重定向
	    }
	    
	    #若访问目录、文件不存在，重定向到404页面
	    if (!-e $document_root$fastcgi_script_name) {
	        rewrite ^.*$ /404.html break;
	    } 
	    root html;
	    index index.html
	}
	
以xx.com/dsafsd.html这个不存在页面为例，我们观察访问日志，日志中显示的访问路径，依然是GET /dsafsd.html HTTP/1.1。提示:服务器内部的rewrite和302跳转不一样。
跳转的话URL都变了，变成重新http请求404.html，而内部rewrite， 上下文没变，
就是说 fastcgi_script_name 仍然是 dsafsd.html，因此会循环重定向。

set 是设置变量用的， 可以用来达到多条件判断时作标志用，达到apache下的 rewrite_condition的效果

	#使用set方式防止重定向死循环
	if ($http_user_agent ~* msie) {
	    set $isie 1;
	}
	
	if ($fastcgi_script_name = ie.html) {
	    set $isie 0;
	}
	
	if ($isie 1) {
	    rewrite ^.*$ ie.html;
	}
	
##### Nginx与PHP配合

	#当碰到访问 .php 的时候时候
	location ~ \.php$ {
	    root html;
	    #把请求的信息转发给9000端口的PHP进程
	    fastcgi_pass   127.0.0.1:9000;
	    fastcgi_index  index.php;
	    #告诉php进程想运行哪个php文件 
	    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
	    include        fastcgi_params;
	}
	
	#1: 碰到php文件,
	#2: 把根目录定位到 html,
	#3: 把请求上下文转交给9000端口PHP进程,
	#4: 并告诉PHP进程,当前的脚本是 $document_root$fastcgi_scriptname
	# (注:PHP会去找这个脚本并处理,所以脚本的位置要指对)

##### 反向代理 + 负载均衡

用nginx做反向代理和负载均衡非常简单。

只需要两个配置， 1个proxy， 1个upstream，分别用来做反向代理，和负载均衡。

以反向代理为例，nginx不自己处理php的相关请求，而是把php的相关请求转发给apache来处理

	#将php程序交给8080端口的apache处理，实现动静分离
	location ~ \.php$ {
	    proxy_pass  http://xxx.xxx.xx:8080 
	}
	
	
	http {
    ...
    #负载均衡服务器池
    upstream xxx {
        server 127.xx.xx.xx1;
        server 127.xx.xx.xx2;
    }
    server {
        liseten 80;
        server_name localhost;
        location / {
            #用户真实IP
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass http://xxx     #upstream 对应自定义名称
            include proxy.conf;  
        }
    }
	}