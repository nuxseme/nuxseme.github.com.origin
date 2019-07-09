---
title: nginx-常见问题
categories: nginx
date: 2019-07-08 16:26:19
---


#### 相同 server_name 多个虚拟主机优先级
	#当出现虚拟主机域名相同的情况，重启nginx时，会出现警告⚠️处理，但是并不不会阻止nginx继续使用
	
	server {
	    listen 80;
	    server_name www.baidu.com
	    ...
	}
	
	server {
	    listen 80;
	    server_name www.baidu.com
	    ...
	}
	
	...
	
	优先选择最新读取到的配置文件，当多个文件是通过include时，文件排序越靠前，越早被读取
	


#### location 匹配优先级

	 =        #进行普通字符精确匹配，完全匹配
	 ^~       #进行普通字符匹配，当前表示前缀匹配
	 ~\~*     #表示执行一个正则匹配()
	
	#当程序使用精确匹配时，一但匹配成功，将停止其他匹配
	#当正则匹配成功时，会继续接下来的匹配，寻找是否还有更精准的匹配
	
#### try_files的使用

按顺序检查文件是否存在

	location / {
	    try_files $uri $uri/ /index.php;
	}
	
	#先查找$uri下是否有文件存在，若存在直接返回给用户
	#若$url下没有文件存在，再次访问$uri/的路径是否有文件存在
	#还是没有文件存在，交给index.php处理
	
	例：
	location / {
	    root /test/index.html
	    try_files $uri @test
	}
	
	location @test {
	    proxy_pass http://127.0.0.1：9090;
	}
	
	#访问 / 时，查看 /test/index.html 文件是否存在
	#若不存在，让9090端口的程序去处理这个请求

#### alias和root的区别

	location /request_path/image/ {
    root /local_path/image/;
	}
	
	#当我们访问 http://xxx.com/request_path/image/cat.png时
	#将访问 http://xxx.com/request_path/image/local_path/image/cat.png 下的文件
	
	location /request_path/image/ {
	    alias /local_path/image/;
	}
	
	#当我们访问 http://xxx.com/request_path/image/cat.png时
	#将访问 http://xxx.com/local_path/image/cat.png 下的文件
#### 获取用户真实IP
当一个请求通过多个代理服务器时，用户的IP将会被代理服务器IP覆盖

	#在第一个代理服务器中设置
	    set x_real_ip=$remote_addr
	#最后一个代理服务器中获取
	    $x_real_ip=IP1
	    
#### Nginx 常见错误码

	413 Request Entity Too Large    #上传文件过大，设置 client_max_body_size
	503 bad gateway                 #后端服务无响应
	504 Gateway Time-out            #后端服务执行超时