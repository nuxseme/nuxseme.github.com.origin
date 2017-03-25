---
title: https
categories: http
date: 2017-03-25 09:40:15
---


### letsencrypt

	//git下载letencrypt 源码
	$git clone https://github.com/certbot/certbot.git
	$cd letencrypt/
	$sudo ./letsencrypt-auto --help all//安装依赖
	$./letsencrypt-auto certonly --standalone
	//填写 email  domain 多个域名 空格隔开

    /etc/letsencrypt ]$ ll
	total 40K
	drwxr-xr-x   8 root root 4.0K Mar 25 03:05 .
	drwxr-xr-x. 92 root root  12K Mar 25 04:51 ..
	drwx------   3 root root 4.0K Mar 25 02:36 accounts
	drwx------   3 root root 4.0K Mar 25 03:05 archive
	drwxr-xr-x   2 root root 4.0K Mar 25 03:05 csr
	drwx------   2 root root 4.0K Mar 25 03:05 keys
	drwx------   3 root root 4.0K Mar 25 03:05 live
	drwxr-xr-x   2 root root 4.0K Mar 25 03:05 renewal

	tips:
	1 确保域名能被解析到
	2 关闭80 443端口
	3 关闭防火墙(如遇到问题)
	4 腾讯云需要在安全组添加443端口权限

### nginx.conf

	#http 自动重定向到https
	server {
	        listen 80;
	        server_name nuxse.com www.nuxse.com;
			return 301 https://$host$request_uri;
	        }

	listen 443 ssl;
	ssl on;
	#域名下的证书文件
	ssl_certificate /etc/letsencrypt/live/nuxse.com/fullchain.pem;
	ssl_certificate_key /etc/letsencrypt/live/nuxse.com/privkey.pem;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_prefer_server_ciphers on;

	//重启nginx


> letsencrypt可以设置自动续签