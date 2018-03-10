---
title: gitlab-install
categories: gitlab
date: 2017-03-18 18:06:04
---


### 环境
> os : CentOS Linux release 7.2.1511 (Core)  
> gitlab: 8.17.3  
> gitlab [down-page](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/)

### 安装流程

#### 下载文件
	$ll
	-rwxr-xr-x   1 root root 270885825 12月 22 21:47 gitlab-ce-8.17.3-ce.0.el7.x86_64.rpm
	
#### rpm 安装
	$rpm -ivh gitlab-ce-8.17.3-ce.0.el7.x86_64.rpm
	//通常gitlab将会安装到/opt/gitlab目录下
	///var/log/gitlab  日志文件
	///etc/gitlab/gitlab.rb 配置文件
	
	//rpm
	rpm -ivh xxx 安装
	rpm -e xxx	 移除
	rpm -qa 列出所有安装的包
	rpm -q xxx  查询是否安装了包

<!--more-->
#### 修改host配置
	
	$vim /etc/gitlab/gitlab.rb
	13 external_url 'http://xxx.xx.xx.xx'//指定自己访问的ip或者host
	
#### 重新配置
	$gitlab-ctl reconfigure //修改url之后需要重新配置
	
### gitlab-ctl command

	$gitlab-ctl help //帮助文档
	$gitlab-ctl status //查看gitlab状态
	$gitlab-ctl stop 
	$gitlab-ctl start | restart |reconfigure
	$gitlab-ctl tail unicorn //跟踪unicorn 状态
	
### tips
* 502 最好确保其他的http服务器已经关闭了，很多502都是由于端口被占用导致  
* {secret_file} don't match the value of Gitlab  在gitlab-ctl reconfigure的阶段出现这种情况。由于安装了低版本的gitlab,删除目录的时候没有删除干净，到时新版本安装的时候文件的secret_file 不是新版本的  
* 出现故障 可以查看/var/log/gitlab下的各种日志  


