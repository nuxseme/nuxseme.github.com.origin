---
title: redis与memcached的区别及使用场景
categories: cache
date: 2018-06-15 11:49:04
---


### redis

#### 需要复杂数据类型支持

* redis string  hash set zset list
* memcache string

#### 需要持久化

* redis不是mysql 固化数据不一定有mysql做的更好
* redis宕机重启，从硬盘回复数据。不会将压力瞬间压到数据服务器上
* redis重启可能导致数据不一致问题，一般针对特定的场景[允许数据不一致?]

#### 高可用

* redis 内置集群，故障转移对客户端透明
* memcache 需要客户端搭建集群服务

#### Value限制

* redis 512M
* memache 1M[可编译重新设置]

### memcache

kv数据，并发量请求量大

#### 内存机制

* redis 临时分配，碎片，分配时间 
* memcache 预分配内存池

#### 虚拟内存

* redis 内存超量,采用swap,需要把冷盘的数据加载到内存
* memcache 所有数据都在物理内存

#### 网络模型

* memcache redis 非阻塞IO 
* redis 排序，集合复杂操作会阻塞

#### 线程模型

* reids 单线程，原子性，无法复用多核性能
* memcache Master-Worker 多线程，worker可能会出现锁冲突

### 总结

* 在通常的项目中其实用redis memcache  没什么区别。一般根据已定的cache使用即可
* 在最初选型的时候需要结合项目来选定cache,其实中期也可以替换，不用太纠结
* 项目瓶颈或者疑难杂症往往不在这，通常在项目中妥协的方案不是因为它够好，而是有更重要的东西还没解决好


