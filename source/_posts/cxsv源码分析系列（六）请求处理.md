---
title: cxsv源码分析系列（六）请求处理
date: 2016-06-27 23:53:19
categories: 
- cxsv
---
### 概述
>从客户端发起请求到应用服务器响应返回数据整个过程在AppServer和HttpServer已经提到过，这里完整系统的梳理下

### 端口监听
> 服务器实例化之后就监听了设定的端口。这里的请求可以来自客户端直接访问，也可以用nginx和apache作为代理转发。

<!--more-->
### HttpServer响应请求
> HttpServer实现了swoole扩展的swoole_http_server的回调函数。
> 开启服务器之后，onRequest函数响应端口接受的请求。
> 
```
public function onRequest(\swoole_http_request $rq,\swoole_http_response $rs)
```
>  
```
//响应请求的插件
apply_action('on_request');
```
>  
```
//回调到AppServer->start
call_user_func($this->_onRequest,$rq,$rs); 
```

### AppServer解析控制器
>应用服务器继承http服务器的特性，请求回调到AppServer->start下进行下一步处理
>  
```
public function start($serv){
            $path_info = Common::getActionUrl();
            $request_methods = array('get'=>'doGet','post'=>'doPost','add'=>'doAdd','update'=>'doUpdate','put'=>'doUpdate','delete'=>'doDelete');
            //解析method
            $method = 'get';
            $path_info = trim($path_info,'/');
            $reqs = explode('/',$path_info);
            if(!empty($path_info) && count($reqs)==1){
                $act = $reqs[0];
                if(is_post()){
                    $method = 'post';
                }elseif(is_put()){
                    $method = 'put';
                }elseif(is_delete()){
                    $method = 'delete';
                }
            }elseif(count($reqs)>1){
                $m = array_pop($reqs);
                if(in_array($m,array_keys($request_methods))){
                    $method = $m;
                }else{
                    $reqs[] = $m;
                    if(is_post()){
                        $method = 'post';
                    }elseif(is_put()){
                        $method = 'put';
                    }elseif(is_delete()){
                        $method = 'delete';
                    }
                }
                array_walk($reqs,function(&$v,$k){$v = ucwords($v);});      
                //解析act
                $act = join('\\',$reqs);
            }else{
                $act = 'index';
            }
            $act = ucwords($act);
            $act_class = '\\Act\\'.$act;
            //解析模板文件
            $act_file = APP_PATH.'Act/'.str_replace('\\','/',$act).'.php';
            if(!in_array($method,array_keys($request_methods)) || !is_file($act_file) || !class_exists($act_class)){
            	return $this->http404();
            }
            //开启缓存
            ob_start();
            apply_action('app_start',$act_file);
            $this->session->start();
            $act_obj = new $act_class($this,$method);
            $handler = $request_methods[$method];
            //执行请求的act->method
            $data = $act_obj->$handler();
            $data_type = gettype($data);
            if($data_type=='array' || $data_type == 'object'){
                $data = var_export($data,true);
            }
            if(($obcache = ob_get_contents())!==false){
                $data = $obcache . $data;
                //清空缓存
                ob_end_clean();
            }
            apply_action('on_response',$data);
            //返回数据
            $this->response($data);
        }
```
















