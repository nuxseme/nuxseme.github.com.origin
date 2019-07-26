---
title: predis源码阅读
date: 2019-07-26 10:19:40
categories: redis
---

> 测试redis集群，对predis客户端的路由好奇

#### 关键概念和类

* client 
> 提供统一的服务入口，构造函数初始化connection，options,profile
> 魔术方法,对于传入的指令转到createCommand  execCommand 方法执行

* options
> 类似yii application 服务挂载树的概念。提供connection，cluster等服务的默认指定类并初始化

* conecntion
> 接受具体的ip port 连接服务器，发送请求，接受响应

* command
> 处理command参数，处理对应command的响应

#### RedisCluster
处理集群的路由逻辑

1. 接受到command对象，猜测当前key的节点 guessNode 这一步精妙
2. 执行命令，成功则返回，失败解析响应请求
3. 关键词MOVED 跳转到服务器返回的连接节点
4. 成功则返回，失败抛出异常

#### 流程

1. 初始化client，需要连接ip port 等其他数据，接受指定命令。初始化options,connection,profile
2. _iscall 跳转到createCommand (创建command对象) execCommand
3. RedisCluster 计算node 执行command，失败策略，MOEVD测试 ASK策略
4. connection 发送请求，返回响应

