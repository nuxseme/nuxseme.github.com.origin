---
title: nginx-开启缓存
categories: nginx
date: 2017-03-01 16:06:07
---


	//图片
	location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ { 
        access_log   off; 
        expires      30d;
	}
	//css js 
	location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
    	access_log   off;
    	expires      24h;
	}
	//html
	location ~* ^.+\.(html|htm)$ {
        expires      1h;
	}
	//字体
	location ~* ^.+\.(eot|ttf|otf|woff|svg)$ {
        access_log   off;
        expires max;
	}

<--more-->
	
	Request URL:http://cache.com/BBC-02.jpg
	Request Method:GET
	Status Code:200 OK
	Remote Address:192.168.107.231:80
	Response Headers
	view source
	Cache-Control:max-age=2592000
	Connection:keep-alive
	Content-Encoding:gzip
	Content-Type:image/jpeg
	Date:Wed, 01 Mar 2017 09:59:44 GMT
	ETag:W/"58b69af1-5d13b"
	Expires:Fri, 31 Mar 2017 09:59:44 GMT
	Last-Modified:Wed, 01 Mar 2017 09:57:05 GMT
	Server:nginx/1.11.3
	Transfer-Encoding:chunked

	Request URL:http://cache.com/BBC-02.jpg
	Request Method:GET
	Status Code:304 Not Modified
	Remote Address:192.168.107.231:80
	Response Headers
	view source
	Cache-Control:max-age=2592000
	Connection:keep-alive
	Date:Wed, 01 Mar 2017 10:00:06 GMT
	ETag:"58b69af1-5d13b"
	Expires:Fri, 31 Mar 2017 10:00:06 GMT
	Last-Modified:Wed, 01 Mar 2017 09:57:05 GMT
	Server:nginx/1.11.3

* tips
> 针对图片，有时可能会出现这样的场景。希望url由nginx交给php去处理，又想针对图片设置缓存  
> 这个时候如果设置了上面的图片缓存，php将收不到url，亦不会处理其他的业务逻辑。需要到php层  
> 设置图片的缓存 http code 304  etag  lastModify等