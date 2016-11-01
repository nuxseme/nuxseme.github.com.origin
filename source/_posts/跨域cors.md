---
title: 跨域cors
date: 2016-9-24 14:09:49
categories:
  - web
---

### 什么叫跨域

> 本站的js请求其他域名下的接口数据就会出现跨域报错，虽然是报错，但是浏览器实际发出了请求，在控制台可能不显示，请求也经过服务器路由到达了目标服务器，请求整个过程是有效的。在请求返回阶段，浏览器检测是否跨域
	
	$.ajax({

		'url' : 'http://destination/index/index',
		'dataType' : 'json',
		'data':{username:"hello"},
		success:function(response){
			console.log(response);
		}
	});

	XMLHttpRequest cannot load http://destination.com/index/index. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://resource.com' is therefore not allowed access.

<!--more-->

### Access-Control-Allow-Origin
>在目标服务器上添加Access-Control-Allow-Origin返回的header，服务器框架可以设定，也可以手动设定

	header("Access-Control-Allow-Origin:http://resource.com");

	public function actionIndex()
    {

       header("Access-Control-Allow-Origin:http://resource.com");
       return json_encode(['message' => 'ok you  can get message']);
    }

	//console
    Object {message: "ok you  can get message"}

### JSONP
> 需要服务器配合，服务器接收参数，处理调用callback,js实现callback

	public function actionHello()
    {
        $callback = $_GET['callback'];
        echo $callback.'('.json_encode(['message' => 'ok you  can get message']).')';
    }

    <script type="text/javascript"> 
	function getmessage(response){
		console.log('回调方式传递');
		console.log(response);
	}
	</script>
	<script src="http://middleware.b2b.com.cn/index/hello?callback=getmessage"></script>
	//console.log
	回调方式传递
    Objectmessage: "ok you  can get message"__proto__: Object
