---
title: service与systemctl
categories: centos
date: 2017-04-22 14:17:38
---


### service 

- 目录：/etc/init.d/
- 本质上是shell脚本
- chkconfig --[add|list] [name]

### systemctl [推荐]

- 目录(通常)：/lib/systemd/[system|user]
- chmod +x [name]

### systemctl配置nginx示例

	[Unit]
	Description=The NGINX HTTP and reverse proxy server
	After=syslog.target network.target remote-fs.target nss-lookup.target
	
	[Service]
	Type=forking
	PIDFile=/run/nginx.pid //对应到ngin.conf的pid路径，这里设置错误 采用systemctl status nginx.service 查看 
	//error message:PID file /run/httpd/httpd.pid not readable (yet?) after start.
	ExecStartPre=/opt/software/nginx/sbin/nginx -t -c /opt/software/nginx/conf/nginx.conf //对应指定的nginx.conf
	ExecStart=/opt/software/nginx/sbin/nginx -c /opt/software/nginx/conf/nginx.conf
	ExecReload=/bin/kill -s HUP $MAINPID
	ExecStop=/bin/kill -s QUIT $MAINPID
	PrivateTmp=true
	
	[Install]
	WantedBy=multi-user.target