---
title: nginx用户认证配置
categories: nginx
date: 2017-11-19 06:53:10
---


### 配置
> 配置段server location http .. 

	server {
     listen       8083;
     server_name   localhost;
     root  /opt/work/nginx_auth;
     #提示
     auth_basic "用户认证";
     #用户名密码保存文件 相对nginx.conf
     auth_basic_user_file httpasswd;
     autoindex on;
     location ~ .*\.(php|php5)?$ {
        fastcgi_pass  127.0.0.1:9000;
        fastcgi_index index.php;
        include fastcgi.conf;
     }
	}
	
### 密码设置

	$openssl passwd -crypt 123456 > httpasswd
	$ cat httpasswd #手动在前面加上用户名
	hello:87/3iIoSW7IUA