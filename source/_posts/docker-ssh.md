---
title: docker-ssh
categories: docker
date: 2018-03-14 15:15:03
---


### dockerfile

> 预先生成authorized_keys

	FROM centos
	#安装
	RUN yum update -y \
	&&  yum install -y openssh-server \
	&&  mkdir /root/.ssh \
	&&  ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
	#复制外部的sshd_config文件

	ADD sshd_config /etc/ssh/
	ADD authorized_keys /root/.ssh/

	EXPOSE 22

	CMD [ "/usr/sbin/sshd","-D" ]

### run

	$docker run -p 5000:22 --name centos-ssh -d centos-ssh
	
### cli

	$ssh -p 5000 root@127.0.0.1