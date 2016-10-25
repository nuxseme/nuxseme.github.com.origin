---
title: docker
categories:
  - docker
---

### docker

>docker类似github,我们将项目用github分布式管理起来，通常github仓库作为中心仓库，git客户端在终端操作。docker hub类似github的作用，docker client 类似于git,git管理代码，docker管理运行环境。

#### docker 安装

	$yum -y install docker #centos7下

#### docker command

	$docker ps -a|-l #列出正在运行的容器，-a all  -l last
	$docker run --ti --name containername image # -t terminal 挂起伪终端 -i 绑定io --name containername 
	$docker images #列出所有镜像
	$docker-enter contaniername| id # 进入正在运行的容器
	$docker kill containername|id #停止并删除容器
	$docker stop containername | id 停止容器
	$docker rm  containername|id #删除容器
	$docker rmi containername|id #删除镜像
	$docker commit -m 'comment' containername|id #容器生成镜像
	$docker run --link  -v #容器间相互连接 挂载数据共享文件 宿主机 容器修改双方都生效
	
#### 注解

>镜像，类似一个class,是一个静态的代码文件。容器类似一个new的对象。容器也可细分，一个是由镜像create的，这里在镜像的基础上加上了一个读写的层。 一个是create+start，这个包含运行的空间，进程等运行态

>

### Dockerfile
	
>yum clean all 清除yum安装过程中的冗余
 make clean 清除make过程的代码

 

yum clean all
make clean
docker-clean docker-enter docker ps -a|-l
数据容器不用保持运行态
