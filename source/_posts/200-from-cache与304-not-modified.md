---
title: 200-from-cache与304-not-modified
date: 2017-03-02 11:55:58
categories: web
---

>enter: 在新的tab键入url  请求新的数据，尽量使用本地已缓存数据，200 from cache  
>刷新：f5 点击重新加载按钮 在相同tab键入url  重新请求新的数据，304 not modified  
>强制刷新：ctrl+f5   重新请求新的数据 200  
>tips:chrome

	enter  直接访问（地址栏地址） 间接访问（html 引用css js 图片）  200 from cache
	Request Method:GET
	Status Code:200 OK (from disk cache)//或者200OK (from memory cache)
	Remote Address:192.168.107.231:80

	未发送请求头

	刷新  直接访问（地址栏地址）304  间接访问（html 引用css js 图片）200 from cache
	Request Method:GET
	Status Code:304 Not Modified
	Remote Address:192.168.107.231:80

	Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Accept-Encoding:gzip, deflate, sdch
	Accept-Language:zh-CN,zh;q=0.8,en;q=0.6
	Cache-Control:max-age=0
	Connection:keep-alive
	Cookie:__lnkrntdmcvrd=-1; TT-IMM=7hgff52ipi03pusa780udrm693
	Host:imagesmanager.com
	If-Modified-Since:Fri, 02 Dec 2016 17:53:38 GMT
	Upgrade-Insecure-Requests:1
	User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36

	强制刷新 直接访问（地址栏地址）304  间接访问（html 引用css js 图片）200
	Request Method:GET
	Status Code:200 OK
	Remote Address:192.168.107.231:80

	Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Accept-Encoding:gzip, deflate, sdch
	Accept-Language:zh-CN,zh;q=0.8,en;q=0.6
	Cache-Control:no-cache
	Connection:keep-alive
	Cookie:__lnkrntdmcvrd=-1; TT-IMM=7hgff52ipi03pusa780udrm693
	Host:imagesmanager.com
	Pragma:no-cache
	Upgrade-Insecure-Requests:1
	User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36