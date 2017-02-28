---
title: RESTful
categories: web
date: 2017-02-18 09:21:54
---


#### 理解
>RESTful 表现层状态转移。sc架构下，server保存数据状态，client发送查询，修改，删除，添加等请求。这里的表现层状态指服务器资源在client的展现状态。转移是指我们通过http请求修改服务资源后，表现层的状态改变
	
#### 要点

> 1 避免url包含动词，有些需求可以用服务代替，比如转账，transaction
> 2 考虑api.example.com vs example.com/api
> 3 api版本 example.com/api/v1/user/1

#### 幂等性

> 1 get  多次查询同一资源 服务器资源状态不会改变
> 2 delete 重复删除同一资源，服务器最终资源结果一样
> 3 put 重复提交同一个修改请求，服务器资源状态不会改变
> 4 post  非幂等，服务器多次创建新的资源
