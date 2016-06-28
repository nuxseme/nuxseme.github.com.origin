---
title: cxsv源码分析系列（三）服务器启动
date: 2016-06-26 23:26:22
categories: 
- cxsv源码分析
---
### 概述
框架启动很简单，php执行单入口脚本即可，有多个项目可以自己写个简单的shell脚本。启动之前请确认安装swoole，在指定目录(用户可以修改)有当前应用的配置文件。根据启动时报错信息作相应的安装扩展，修改配置文件即可。

### 启动命令
```
#规划好应用名称和配置，在appServer.php(启动服务的单入口文件)中会设定应用名称为环境变量
$php appServer.php app_name
```
<!--more-->
```
if (empty($app_name = $argv[1])) {
    exit("Usage: appServer.php app_name" . PHP_EOL);
}
```
```
//读取配置文件
if(!is_dir('/etc/cxsv/')){
	mkdir('/etc/cxsv/');
}
$config_file = '/etc/cxsv/'.$app_name.'.ini';
if(!file_exists($config_file)){
	$config_file = __DIR__.'/etc/'.$app_name.'.ini';
}
if (!is_file($config_file) || false === $config = parse_ini_file($config_file, true)) {
    echo 'the config file [/etc/cxsv/'.$app_name.'.ini] no such , or cat not parsed' . PHP_EOL;
    return;
}
#这部分代码可以自己修改，当前设定在/etc/cxsv目录下和框架目录/etc下
```


### 应用配置文件

```
$php createConfig.php app_name  #应用配置文件根据swoole.ini拷贝
```

```
#应用配置被加载成数组，在httpServer内部某些参数可以重新设定
;守护进程化
daemonize=1

;工作进程数
worker_num=4

;异步任务进程数
task_worker_num=4

;最大请求数，超过此数目，会关闭WORK进程并重新创建，防止内存泄漏
max_request = 100000

;最大连接数，超过此数量后，新进入的连接将被拒绝
max_conn=51200

[server]
;应用根目录
document_root = "/home/www/{app_name}"

;重命名进程
process_rename = 1

;监听IP地址
host = 127.0.0.1

;绑定端口
port = {port}

;keepleve
keepalive=1

;启用定时器
enable_timer = 1

[session]
type = memcache
;如果没有指定单独储存，则用[cache]设置的
;memcache = 127.0.0.1:11211:1:1,127.0.0.1:11211:2:1
session_life = 2880
;session前缀
;prefix = 

;cookie名称，默认为CXSESSID
;cookie_name =

[app]
;编码
charset = utf-8

;应用子目录，在前后台分开目录的情况下有用，否则请保持与document_root一致
app_path = /home/www/{app_name}

[cache]
;缓存类型，目前只支持memcache
type = memcache

;memcache服务器的IP地址列表，多个之间用关角逗号分隔。支持分布式
;host:port:weight:persistent
host = 127.0.0.1:11211:1:1,127.0.0.1:11211:1:1

[redis]
;主机IP
host = 127.0.0.1

;端口
port = 6379

;密码
auth = 

[db]
;数据库配置
;格式：host:port:user:password:dbname

;读写分离
;read = 127.0.0.1:3306:user:password:dbname
;write = 127.0.0.1:3306:user:password:dbname

;单机
host = 127.0.0.1:3306:{app_name}:{app_name}123:{app_name}

;mongodb
[mongodb]
;无密码的链接
host = 127.0.0.1:27017

;带用户名密码的链接
;host = mongodb://{app_name}:123@127.0.0.1

;多个数据库
;host = mongodb://127.0.0.1:27017,127.0.0.1:27018

;数据库/集
db = {app_name}

##以上截取了部分配置项
```

### 启动流程
#### appServer.php
>扩展检测  
>加载配置文件  
>定义环境变量  
>创建应用目录  
>加载全局函数 
>  
```
require __DIR__.'/load.php';
##load.php
```
>  
```
#包含自动加载器
require LIB_PATH.'Loader.php';
//注册命名空间
\Lib\Loader::setRootNS('Lib',LIB_PATH);
\Lib\Loader::setRootNS('Mod',MOD_PATH);
//注册自动加载函数
spl_autoload_register('\\Lib\Loader::autoload');   
```

>创建应用服务器实例
>
```
$server=new \Lib\AppServer($config);
```

>设置请求回调
>
```
$server->setProcReqFun(array($server,'start'));
```

>挂载全局树  
>
```
$php = &$server;
```

>启动  
>
```
$server->run();
```

#### AppServer.php
> 调用HttpServer构造函数，设定配置，日志，脚本关闭句柄
>
```
public function __construct($config){
        $this->config = $config;
        $this->loger = new EchoLog('');
        register_shutdown_function(array($this, 'handleFatal'));
    }
```

>注册命名空间  
>设置错误句柄
>
```
set_error_handler(array($this,'errorHandler'));
```

#### HttpServer.php
> function run  
> 实例化swoole_http_server  
> 注册事件
> 
```
$server->on('Start',array($this,'onStart'));
$server->on('ManagerStart', array($this,'onManagerStart'));
$server->on('ManagerStop', array($this,'onManagerStop'));
$server->on('WorkerStart',array($this,'onWorkerStart'));
$server->on('Request', array($this, 'onRequest'));
$server->on('Close', array($this, 'onClose'));
$server->on('Shutdown', array($this, 'onShutdown'));
$server->on('Task', array($this, 'onTask'));
$server->on('Finish', array($this, 'onFinish'));
$server->on('WorkerStop',[$this,'onWorkerStop']);
$server->on('WorkerError',[$this,'onWorkerError']);
// $server->on('Timeout',[$this,'onTimeout']);
$server->start();#服务器启动
```

自此服务器启动完毕，这时候生成进程
master->manager->work
根据设定的定时器和任务还会开启定时器进程和任务进程
