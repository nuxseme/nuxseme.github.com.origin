---
title: cookie
categories:
  - null
date: 2016-09-24 14:11:24
---


> 1 cookie 绑定域名 与  路径
  2 当前域名只能设定当前域或者父级域b.a.com  设定cookie的域可为a.com,b.a.com，访问a.com会带上设定的cookie
  3 未设定cookie的域 cookie默认绑定在请求的域+路径  a.com/path  绑定在a.com  a.com/path/ 绑定在a.ocm/path

<!--more-->
测试环境

a.com

	location /pass/{
        proxy_pass http://b.com/;
    }

    location /rewrite/{
        rewrite  ^(.*)$  http://b.com/;
    }


分别设置 a.com b.com的cookie

	header('Set-Cookie:domain=a.com');

访问b.com   $_COOKIE  [domain] => b.com   this is b.com page
访问a.com   $_COOKIE  [domain] => a.com   this is a.com page  
访问a.com/pass/  $_COOKIE [domain] => a.com this is b.com page
访问a.com/rewrite/ $_COOKIE [domain] => b.com this is b.com page

查看b.com 下的cookie  b.com/ => domain:b.com  
查看a.com 下的cookie  a.com/ => domain:a.com


设置b.com 并访问a.com/pass/ 然后关闭cookie设置

 	header('Set-Cookie:domain=ba.com');

访问b.com   $_COOKIE  [domain] => b.com   this is b.com page
访问a.com   $_COOKIE  [domain] => a.com   this is a.com page  
访问a.com/pass/  $_COOKIE [domain] => ba.com this is b.com page
访问a.com/rewrite/ $_COOKIE [domain] => b.com this is b.com page

查看b.com 下的cookie  b.com/ => domain:b.com  
查看a.com 下的cookie  a.com/ => domain:a.com  
					  a.com/pass => domain:ba.com



