---
title: TTFB过长bug追踪
categories: php
date: 2019-05-20 09:27:36
---


#### 问题描述

> 前端js请求并发很慢，单个请求很快，遇到请求外部服务超时的时候每个接口都很慢。正常来讲，nginx+php-fpm的模式下，某一个接口慢不应该影响到其他接口，数据库没有锁表，没有文件竞争等因素


#### 开启日志

> nginx request\_time  upstream\_response\_time
> upstream\_response\_time:从 Nginx 建立连接 到 接收完数据并关闭连接
> request\_time:从 接受用户请求的第一个字节 到 发送完响应数据   

> php-fpm slow\_log error_log 开启pid

> php记录接口耗时

#### 排除nginx层

> 比较网页的Time 和 nginx request\_time  和  upstream\_response\_time 慢接口的请求php返回的数据存在延时

#### 排除代码逻辑

> 在接口入口阶段截断，设置sleep模拟时间消耗，问题仍然存在且能稳定复现

#### 问题应该在php-fpm和php之间

> 查看进程消耗
> 查看机器消耗
> 查看php-fpm 工作模式

#### strace pid 查看进程堆栈

> php-fpm 设定超时并打开slow\_log 日志格式记录下进程的pid
> 发现进程之间竞争session文件的读写锁
> 查看php.ini session.auto\_start = 1

#### 问题症结

> php进程竞争session文件的读写锁。由于session.auto\_start自动打开，每个请求都会执行session\_start()的逻辑
> 为了防止进程复写session文件，加了读写锁，变相的将并发请求变为了串行请求

#### 解决方案

> 这里的解决办法很简单，关闭了session.auto\_start




