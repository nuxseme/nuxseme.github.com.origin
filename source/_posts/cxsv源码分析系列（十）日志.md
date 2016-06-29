---
title: cxsv源码分析系列（十）日志
categories:
  - cxsv源码分析
date: 2016-06-29 20:42:04
---
### 概述
>记录日志的方式有很多种，文件，mysql,mongodb.框架中的日志采用文本和mongodb结合，文本主要记录服务器应用的日志，比如nginx日志，mysql日志，mongodb日志，php日志等。mongodb主要用来记录项目应用的日志。日志分为很多中，debug,error分别是调试和错误的集合，其他的集合通常来做数据存储。

### m_log

>  
```
#默认写到debug集合里面，如果需要独立成单独的集合方便查看可以传递第二个参数集合名称
/**
* 向mongodb写日志
* @param string $msg 日志消息
* @param string $collection 自定义要写到哪个集合  默认为 debug_log
* @return bool
*/
function m_log($msg,$collection=''){
  try{
    $collection || $collection = 'debug_log';
    $data = [
      'datetime' => date('Y-m-d H:i:s'),
      'info' => $msg,
    ];
    return mongodb($collection)->insert($data);
  }catch(\Exception $e){
    return false;
  }
}
```
<!--more-->
### em_log

>  
```
#其实这个啥也没干
/**
* 向MONGODB写错误日志
* @param string $msg 日志消息
* @param string $collection 自定义要写到哪个集合  默认为error_log
* @return bool
*/
function em_log($msg,$collection=''){
  try{
    $collection || $collection = 'error_log';
    $data = [
      'datetime' => date('Y-m-d H:i:s'),
      'info' => $msg,
    ];
    return mongodb($collection)->insert($data);
  }catch(\Exception $e){
    return false;
  }
}
```

### mongoldb

>  
```
#自定义日志格式
/**
* 获得一个mongoDB实例
* @param string $table_name
* @return object
*/
function mongodb($table_name='',$is_gridfs=false){
  static $mongodbs = [];
  global $php;
  $table_name = strtolower($table_name);
  $key = $table_name.($is_gridfs ? 'true' : 'false');
  $key = md5($key);
  if(!isset($mongodbs[$key])){
    if((intval(PHP_VERSION))<7){
      $mongodbs[$key] = clone $php->mongodb;
      !empty($table_name) && $mongodbs[$key]->setCollection($table_name,$is_gridfs);
    }else{
      $mongodbs[$key] = clone $php->mongodb7;
      !empty($table_name) && $mongodbs[$key]->setCollection($table_name,$is_gridfs);
    }
  }
  return $mongodbs[$key];
}
```

### swoole_log

>  
```
$swcfg = array_merge(
            [
                'log_file' => '/dev/null',#修改成指定路径
                'worker_num' => 4,
                'max_request' => 100000,
                'max_conn' => 10000,
                'daemonize' => 0,
            ],$this->c('global'));
```

