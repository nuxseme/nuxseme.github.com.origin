---
title: chrome浏览器网页引用额外的js
categories: web
date: 2017-04-17 11:10:13
---


![body_head_js.png](/img/body_head_js.png)
![body_foot_js.png](/img/body_foot_js.png)

- 只在chrome浏览器存在，opera,firefox不存在 => bug定位chrome  
- 所有页面，域名都会被引用上额外的js => 跟域名服务器无关，定位本地环境
- 升级chrome到最新版本，其他环境不会出现 => 跟chrome版本无关

chrome有个性化的插件配置。尝试将所有的插件逐一关闭之后bug消失，未定位到是哪一个插件引起的(后续观察)  

解决办法：重启chrome插件