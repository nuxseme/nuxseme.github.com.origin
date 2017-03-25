---
title: http2.0
categories: http
date: 2017-03-25 09:40:08
---


### nginx 编译安转

	/opt/software/nginx1.11.12/conf/nginxconf.d ]$ nginx -V
	nginx version: nginx/1.11.12
	built by gcc 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC) 
	//确保OpenSSL 版本>1.0.1，否则请求http2无效
	built with OpenSSL 1.1.0e  16 Feb 2017
	TLS SNI support enabled
	configure arguments: --prefix=/opt/software/nginx1.11.12 --with-openssl=../openssl-1.1.0e --with-pcre --with-zlib=../zlib-1.2.11 --with-stream --with-stream_ssl_module --with-http_ssl_module --with-http_v2_module --with-threads

	#修改配置文件
	listen 443 ssl http2;

### 查看http2
	chrome://net-internals/#http2
![chrome](/img/chrome-http2.png)


	$tail -f access
	[25/Mar/2017:11:28:34 +0800] "GET / HTTP/2.0" 200 445

### 效果比对

http1.1
![http1.1](/img/http1.1.png)
http2.0
![http2.0](/img/http2.0.png)