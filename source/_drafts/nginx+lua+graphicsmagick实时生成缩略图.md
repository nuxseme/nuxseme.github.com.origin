---
title: nginx+lua+graphicsmagick实时生成缩略图
date: 2016-11-28 15:14:55
categories: PHP
---

安装

> 安装lua LuaJIT  
> ln -s /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2 设置软连接，不然配置nginx会报错  
> 安装 GraphicsMagick  
> 安装 nginx
> 
	--with-ld-opt=-Wl,-rpath,/user/local/lib --add-module=/opt/download/ngx_devel_kit-0.3.0 --add-module=/opt/download/lua-nginx-module-0.10.7
<!--more-->

配置
	
     //http://lua.img.com:9876/image/1.png.400x300.png
	 location  /image {
			root /opt/project/lua.img.com;
            set $image_root /opt/project/lua.img.com;
            set $file "$image_root$uri";
			//ngx.var.file=/opt/project/lua.img.com/iamge/1.png.400x300.png
			//图片存在 直接返回 图片不存在处理保存并返回结果
            if (!-f $file) {
                        rewrite_by_lua '
								ngx.var.uri=image/1.png.400x300.png
                                local index = string.find(ngx.var.uri, "([0-9]+)x([0-9]+)");
								//index= 13
                                if (index == nil) then
                                   ngx.exit(404);end;
                                local originalUri = string.sub(ngx.var.uri, 0, index-2);
								//originxUri=image/1.png
                                local area = string.sub(ngx.var.uri, index);
								//area=400x300.png
                                index = string.find(area, "([.])");
								//index=7
                                area = string.sub(area, 0, index-1);
								//area=400x300
								print(area)
                                local image_sizes = {"160x160","400x300"};
                                function table.contains(table, element)
                                for _, value in pairs(table) do
                                        if value == element then
                                                return true
                                        end
                                end
                                return false
                        end
						print(table.contains(image_sizes,area))
                        if table.contains(image_sizes, area) then
						//调用gm 处理
						//ngx.var.image_root = /opt/project/lua.img.com
						//originalUri = image/1.png
						//area = 400x300
						//ngx.var.uri= image/1.png.400x300.png
                        local command = "/opt/software/GraphicsMagick/bin/gm convert " ..ngx.var.image_root ..  originalUri  .. " -thumbnail " .. area .. " ".. ngx.var.image_root .. ngx.var.uri;
						print(command)
                        local ret=os.execute(command);
                        else
                        ngx.exit(404);
                        end;
                ';
          }

          expires 7d;
    }


问题
>在测试过程中，出现了一个问题，command = "gm convert " ..ngx.var.image_root ..  originalUri  .. " -thumbnail " .. area .. " ".. ngx.var.image_root .. ngx.var.uri;原本这里的gm是全局的指令，这样设定的时候，在nginx日志中能看到流程处理了，command命令没有执行成功。这里调试了很久，修改为/opt/software/GraphicsMagick/bin/gm就可以了。

>nginx.conf  user root root 确保对文件具有读写权限
