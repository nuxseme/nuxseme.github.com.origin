---
title: php_shutdown
date: 2018-10-12 09:47:44
categories: php
---

>register_shutdown_function 脚本运行结束和触发错误会执行  
>多个function按注册顺序执行  
>可用来记录php的内存溢出等错误  

	register_shutdown_function(function (){
    $e = error_get_last();

    if ($e === null)
        return;

    print_r($e);
	});