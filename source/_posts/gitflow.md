---
title: gitflow
date: 2017-04-05 10:36:10
categories:
---


![gitflow](/img/gitflow.png)

主干分支  
master(1)：稳定发布的分支，具有tag版本标识。只接收hotfixed分支和release分支的合并请求  
develop(1)：开发分支，接收所有分支的合并请求，hotfixed删除之前需要合并到master和develop分支。release分支需要合并到master和develop分支。feature分支需要的会合到develop

辅助分支  
hotfixed(1):热修复分支，解决线上bug    
feature(n):特性分支，开发新功能  
release(1):释放分支，发版  