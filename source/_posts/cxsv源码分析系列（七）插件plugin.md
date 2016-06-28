---
title: cxsv源码分析系列（七）插件plugin
date: 2016-06-28 18:57:32
categories: 
- cxsv源码分析
---

### 概述
>面对频繁的需求改动，项目初期设计考虑不完善是很正常的。需要为项目添加一个新的功能，又不想原有的代码有过多的污染，这个时候可以考虑使用插件。同时在面向切面编程的时候，比如新增用户，在操作完新增操作之后我们可能有很多事情要做，比如消息推送，建立档案，推送优惠券等等一系列操作，这个时候用插件将不同的任务分开实现。这里也可以考虑用异步任务，异步任务是非阻塞的。如果逻辑上需要同步阻塞的，比如推送优惠券的操作一定要放在建档完成之后（假设），这个时候就适合用插件机制。
>    
```
apply_action('on_worker_start',$server,$worker_id);
```
<!--more-->

### HttpServer
>  
```
#master->manager->work work进程用来处理任务 在work进程开始
的时候加载了服务层和应用层的插件
public function onWorkerStart($server,$worker_id){
        $this->loadPlugin();
    }
```
>  
```
/**
    * 加载插件
    * @access private
    * @return void
    */
    #设定的插件目录在服务层和应用层根目录下的plugin下
    将所有开启的插件初始化
    private function loadPlugin(){
        $paths = array('global'=>FRAME_ROOT.'plugin/','app'=>DOCUMENT_ROOT.'Plugin/');
        foreach($paths as $type => $plugin_path){
            if(!is_dir($plugin_path)){
                continue;
            }
            $odir = opendir($plugin_path);
            while($file = readdir($odir)){
                if($file{0}!='.' && ($path=$plugin_path.$file.'/') && ($plugin_file=$path.$file.'.php') && is_file($plugin_file) && (is_file($path.'enabled') || $type == 'global')){
                    include $plugin_file;
                }
            }
            closedir($odir);
        }
    }
```

### load
>  
```
/**
* 添加一个动作*
* @param String $type
* @param Mix $handler
* @param Int $weight
* @return Bool
*/
function add_action($type,$handler,$weight=1){
  global $php;
  $php->plugins[$type][$weight][] = $handler;
  return true;
}
/**
* 执行一批动作
* @param String $type
* @param mix $arg<n>，默认可接受最多8个参数，一般应用应该够了。
* @return void
*/
function apply_action($type,&$arg1=null,&$arg2=null,&$arg3=null,&$arg4=null,&$arg5=null,&$arg6=null,&$arg7=null,&$arg8=null){
  global $php;
  if(isset($php->plugins[$type]) && !empty($php->plugins[$type])){
    $actions = $php->plugins[$type];
    ksort($actions);
    foreach($actions as $action){
      foreach($action as $the_action){
        call_user_func_array($the_action, [&$arg1,&$arg2,&$arg3,&$arg4,&$arg5,&$arg6,&$arg7,&$arg8]);
      }
    }
  }
}
```



 