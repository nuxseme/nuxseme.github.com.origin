---
title: nginx+image_filter实时生成缩略图
date: 2016-11-28 15:12:37
categories: php
---

安装nginx 

>在原有的配置项上加上--with-http_image_filter_module.下面是nginx -V configure参数跟缩略有关的只有--with-http_image_filter_module。其让选项可根据自己的需求添加

	nginx version: nginx/1.10.0
	built by gcc 4.8.5 20150623 (Red Hat 4.8.5-4) (GCC) 
	configure arguments: --prefix=/opt/software/nginx1.10 --with-debug --with-pcre --add-module=/opt/download/echo-nginx-module-0.60 --with-http_image_filter_module --with-ld-opt=-Wl,-rpath,/user/local/lib --add-module=/opt/download/ngx_devel_kit-0.3.0 --add-module=/opt/download/lua-nginx-module-0.10.7

<!--more-->
应用目录
	
	server_name
	-img
	 	-1.png
	-resize_400x400
		-img
			-1.png

配置server

	//访问http://img.com:9876/resize_100x100/img/1.png
	//该规则匹配
	location ~* ^/resize {
                root /opt/project/$server_name;
                set $width 150;
                set $height 100;
                set $dimens "";

                if ($uri ~* "^/resize_(\d+)x(\d+)/(.*)" ) {
                        set $width $1;//ngx.var.width=200
                        set $height $2;//ngx.var.height=200
                        set $image_path $3;//ngx.var.image_path=img/1.png
                        set $demins "_$1x$2";//ngx.var.demins=_200x200
                }

                if ($uri ~* "^/resize/(.*)" ) {
                        set $image_path $1;
                }

                set $image_uri image_resize/$image_path?width=$width&height=$height;
				//ngx.var.image_uri=image_resize/img/1.png?width=200&height=200

				//ngx.var.request_filename=resize_200x200/img/1.png 
				//图片存在 直接返回 不存在代理生成缩略图
                if (!-f $request_filename) {
						//代理到image_resize/img/1.png?width=200&height=200
                        proxy_pass http://127.0.0.1:9876/$image_uri;
                        break;
                }
                proxy_store /opt/project/$server_name/resize$demins/$image_path;
                proxy_store_access user:rw group:rw all:r;
                proxy_set_header Host $host;
                expires      30d;
                #access_log off;
        }

        location /image_resize {
                alias /opt/project/$server_name/;
				//图片处理  返回处理结果
                image_filter resize $arg_width $arg_height;
                image_filter_jpeg_quality 75;
				image_filter_buffer 10m;
                #access_log off;
        }

处理流程

>0 原图http://hostname/img/1.png  
>1 http://hostname/resize_100x100/img/1.png 解析长宽  
>2 查询resize_100x100/img/1.png是否存在 存在返回结果  不存在 代理到 image_resize/img/1.png?width=200&height=200  
>3 location /image_resize 处理图片 并保存结果  


问题

>在测试阶段删除服务器上的文件，访问地址，没有生成新的图片  
Request URL:http://img.com:9876/resize_100x100/img/1.png  
Request Method:GET  
Status Code:304 Not Modified  
强制刷新即可看到结果
