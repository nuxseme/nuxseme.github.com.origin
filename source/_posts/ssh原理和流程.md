---
title: ssh原理和流程
categories: ssh
date: 2017-01-17 15:52:43
---


	版本协商阶段：
	1 client -> server :请求链接
	2 server -> client :ssh版本号
	3 client 比较版本号，比自己高则用自己的版本号，比自己低则用server返回的版本号（确保协商的版本号双方都能支持）
	4 server收到版本号，确定是否继续
	会话，密钥交换阶段：
	5 双方发送自己的算法列表，计算最终使用的算法
	6 server 生成session_id,client 生成 密钥
	7 server发送自己的公钥给client
	8 server发送自己私钥加密后的session_id给client
	9 client现在保存有server的公钥和用公钥解密的session_id
 	10 clinet用server的公钥加密生成的密钥
	11 server收到密钥，用自己的私钥解密，现在双发保存了session_id和密钥
	12 接下来的数据传输采用密钥加密解密，rsa用作大量的数据加密解密性能不足

	密码登录：
	client 用密钥加密自己的username和password
	server收到数据用密钥解密，认证用户的权限，成功登录或者拒绝接入

	公钥登录：
	这里的公钥跟上面协商的公钥不同，这里的公钥是任意一对私钥公钥(通常预先生成，将公钥放到认证文件中，将私钥放到client的.ssh下)
	client发起免密码登录请求，用密钥加密用户和id_ras.pub
	server解密数据到用户目录下的认证文件中查找id_ras.pub,存在则继续认证
	server发送用密钥和公钥加密的质询
	client用密钥和私钥解密质询，用密钥加密质询发给server
	server校验收到的质询，相同认证通过


	ps:
	1 协商阶段的公钥私钥跟认证的公钥私钥不是同一个
	2 数据交流的密钥是对称加密
	3 client 用户目录下哎可以有多个钥匙对用于不同的认证，校验的时候会自动判断采用哪一个钥匙

	centos:
	yum install openssh-server -y #安装ssh
	service sshd start|stop|restart
	
	/etc/ssh下的ssh_host*可以删除，sshd启动会重新生成
	.ssh 下的密钥对可删除，自己生成#ssh-keygen -t rsa -P '' -f xxx
	
	/etc/ssh/sshd_config下有许多配置
	是否允许root登录
	是否允许无密码登录
	认证文件
	mac下超时自动断开设置

	debug：
	ssh -i id_rsa root@127.0.0.1 -vvv
	