---
title: nginx负载均衡
categories: nginx
date: 2017-03-28 10:57:51
---


###vhost 

	upstream loadbalance{  
	#设定负载均衡配置名称
    server 127.0.0.1:8081;  //设定主机1 ip+端口
    server 127.0.0.1:8082;  //设定主机2 ip+端口
    }  
	server {
    listen       80;
    server_name  load-balance.com;
	location / {     
            proxy_pass  http://loadbalance;     
        }     
    access_log  /opt/software/nginx/logs/load-balance.access;
    error_log  /opt/software/nginx/logs/load-balance.error;
	}
	server {
		listen 8081;
		server_name 127.0.0.1;
		index index.html;
		location /{
			root /opt/work/load-balance1.com;
		}
	}
	server {
		listen 8082;
		server_name 127.0.0.1;
		index index.html;
		location /{
			root /opt/work/load-balance2.com;
		}
	}

###测试结果

- 交替显示load-balance1 load-balance2下的index.html
- 可以设置权重，是否参与负载

### 104: Connection reset by peer

>这种错误大抵会出现在本地测试过程中，如果你用了host映射

	upstream loadbalance{  
		#设定负载均衡配置名称
    	server load-balance1.com;  //设定主机1 ip+端口
    	server load-balance2.com;  //设定主机2 ip+端口
    }

	hosts
	127.0.0.1 load-balance1.com
	127.0.0.1 load-balance2.com

这里建议指定ip+port