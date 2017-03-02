---
title: 200-from-disk-cache与200-from-memory-cache
date: 2017-03-02 11:56:44
categories: web
---

> 未深入浏览器源码，只记录现象 
> from disk cache （物理磁盘）和 from memory cache（内存） 都是从缓中拿取  
> Webkit分为主资源（html，下载文件）， 派生资源（imag,js,css） 
> memoryCache 只对派生资源，派生资源的缓存disk memory 都有  
> 第一次请求图片200 from memory cache ，刷新304  
> 新的tab重新请求，disk memory都会出现 