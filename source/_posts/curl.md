---
title: curl
categories: php
date: 2016-11-01 19:49:41
---


    CURLOPT_USERAGENT #设置客户端的代理名称
    CURLOPT_TIMEOUT   #请求超时时间 60
    CURLOPT_CONNECTTIMEOUT #链接超时时间 60
    CURLOPT_RETURNTRANSFER # true 是否以返回值的形式接受数据
    CURLINFO_HEADER_OUT    # true  开启头部详情 默认false
    
    #设置true之后可以debug请求头的信息
    curl_getinfo($curlResource, CURLINFO_HEADER_OUT);
    
    CURLOPT_HEADER         #  true 是否接受响应头部 默认false
    CURLOPT_HTTPHEADER    # array('Expect:') 避免客户端发送一个大的数据包时，
    先询问服务端是否接受，服务端返回响应头部后才发送数据
    CURLOPT_POSTFIELDS  #设置post数据
    CURLOPT_HTTPHEADER  #设置头部数据
    CURLOPT_URL # 设置请求curl
    CURLOPT_CUSTOMREQUEST #设置请求方式