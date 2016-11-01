---
title: cxsv源码分析系列（八）任务
date: 2016-06-28 21:39:53
categories: 
- cxsv
---
### 概述
>插件是同步阻塞的，任务是异步非阻塞的。异步任务有非常合适的场景，逻辑独立，在多个地方适用同一个逻辑，程序需要立即返回，通常是执行时间不能超时等等，异步任务可以将这次的处理投递出去，不需要获得的返回值.  
>  
```
function add_task($task_name,$task_data){
  global $php;
  // 在用户自定义的进程里，是无法调用异步任务
  $data = gzcompress(json_encode(['name'=>$task_name,'data'=>$task_data]),5);
  return $php->http_server->task($data);
}
```
<!--more-->
### HttpServer
>  
```
* 异步任务回调函数
    * @access public
    * @param \swoole_server $serv
    * @param int $task_id
    * @param int $from_id
    * @param string $data
    * @return void
    */
    public function onTask($serv, $task_id, $from_id, $data){
        try{
            $task_data = @json_decode(gzuncompress($data),true);
            if(empty($task_data)){
                return;
            }
            $task_name = $task_data['name'];
            $data = $task_data['data'];
            $params = '';
            #在Task命名空间下静态方式调用run
            $task = '\\Task\\'.ucwords($task_name);
            $task::run($data);
        }catch(\Exception $e){
            $this->log($e->getMessage());
        }
        // $serv->finish();
    }
```