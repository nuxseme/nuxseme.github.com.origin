---
title: php网络数据传递数组键值小数点自动转换为下划线
categories: php
date: 2016-12-30 13:42:04
---


	POST /test HTTP/1.1
	Host: test.com
	Content-Type: application/x-www-form-urlencoded
	Cache-Control: no-cache
	Postman-Token: 32a26481-1ed0-cbc1-3d8d-6dec2ae0f7fa

	a.b=aa
	
	//post 
	Array
	(
	    [a_b] => aa
	)
	//php://input
	a.b=aa


	Content-Type: application/x-www-form-urlencoded
	Cache-Control: no-cache
	Postman-Token: 84cf4641-91e2-0c84-f96f-9e2733366a14
	
	{"a.b":"hello"}

	//post
	Array
	(
	[{"a_b":"hello"}] => 
	)
	//php://input
	{"a.b":"hello"}


	#采用applicaion/json 格式
	Content-Type: application/json
	Cache-Control: no-cache
	Postman-Token: 60a74bcf-c8a6-8a83-c971-e828cebf7472
	
	{"a.b":"hello"}

	//post
	Array
	(
	    [a.b] => hello
	)
	//php://imput
	{"a.b":"hello"}