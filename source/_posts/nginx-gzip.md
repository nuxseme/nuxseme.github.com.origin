---
title: nginx-gzip
categories: nginx
date: 2017-03-01 16:05:04
---

	
	gzip on;//开启gzip
    gzip_min_length 1k;//小于1k 不压缩
    gzip_buffers 4 16k;// 这个太明白  网上说了一堆 没弄懂 后续补上
    #gzip_http_version 1.0;//排除http 1.0版本
    gzip_comp_level 2;//压缩等级 压缩等级1-9 数字越高压，服务器cpu工作量越大，压缩比（可能）会更好  建议配置1/2
    gzip_types text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php;
	//压缩格式 建议不要加上image下的格式
   	gzip_vary on;//和http头有关系，加个vary头，给代理服务器用的，有的浏览器支持压缩，有的不支持，避免浪费不支持的也压缩
   	gzip_disable "MSIE [1-6]\.";//IE对gzip支持不友好

	

#### gzip\_comp\_level

	原始：-rwxrwxrwx. 1 root root  32K Feb 18 15:17 jquery.blueimp-gallery.min.js
	0 31.5 KB
	1 10.1 KB
	2 9.6 KB
	3 9.4 KB
	5 8.5 KB
	9 8.4 KB
	//数据压缩在1|2之后效果不明显了  所以建议gzip_comp_level的配置设为1或者2即可。减少服务器cpu的压力

#### 取消对图片的压缩

	mime 原始大小	压缩大小  减少    %
	jpeg 404 KB		393 KB	 11KB	2.5
	png  215 KB		215 KB	 0      0
	jpg  373KB		351 KB   22KB  5.8
	//未完全测试，未多次大量测试，结果仅做参考
	