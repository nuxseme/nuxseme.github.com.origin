---
title: ssh隧道连接mysql
categories: ssh
date: 2017-07-13 19:19:33
---


>数据服务器A和业务服务器B，设了ip白名单。只能由B访问数据A。本地开发常常需要连接数据库查看数据，可以进入到机器B中，但是B上有没有安装mysql client。可以采用ssh隧道，将本地的mysql 操作映射到B机器上访问A机器的端口上
### 建立隧道
	#开启相应的隧道代理
	ssh -fN -L $local_port:$remote_host:$remote_port -p $port $name@$ip
	
	    FILE='/tmp/.ssh_tunnel_login.sh'
	    echo '#!/usr/bin/expect -f' > $FILE
	    echo 'set timeout 30' >> $FILE
	    echo "spawn $cmd_name " >> $FILE
	        if [ "$pass" != "" ]; then
	            echo 'expect "password:"' >> $FILE
	            echo 'send   '$pass"\r" >> $FILE
	
	        fi
	    echo 'interact' >> $FILE
	    chmod a+x $FILE
	    $FILE

>ssh -fN -L $local_port:$remote_host:$remote_port -p $port $name@$ip 


### mysql connetion

	mysql -h127.0.0.1  -P$admin_local_port -u$admin_user -p$admin_pass -D$db_name
	
	
> mysql -u127.0.0.1 -P5000 -uroot -ppassword -Ddb