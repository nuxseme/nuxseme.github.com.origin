---
title: cxsv源码分析系列（五）HttpServer
date: 2016-06-27 23:49:28
categories: 
- cxsv
---

### 概述
>HttpServer是框架的核心文件，对swoole_http_server的实现。实现了各个注册的回调函数，监听端口并解析请求报文，然后将请求回调到AppServer->start()，在wrok_start中实现了插件的挂载，在onTask回调函数中实现了任务的投递。定时器不能加载任务，进程中不能加载异步任务。
<!--more-->

### HttpServer源码  
```
class HttpServer{

       public function __construct($config){
        $this->config = $config;
        $this->loger = new EchoLog('');
        //注册致命错误句柄
        register_shutdown_function(array($this, 'handleFatal'));
    }
  
    public function run(){
        $server = new \swoole_http_server($this->c('server.host'), $this->c('server.port'));
        $this->serv = $server;
        $server->set($swcfg);
        $this->config = array_merge($this->config,$server->setting);
        
        //注册回调函数
        
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
        $server->start();
    }

    /**
    * 主进程启动时回调函数
    * @access public
    * @param \swoole_server $serv
    * @return void
    */
    public function onMasterStart($serv){
        $this->log(SERVER_NAME."[#master] start");
    }

    /**
    * 管理进程启动时回调函数
    * @access public
    * @param \swoole_server $serv
    * @return void
    */
    public function onManagerStart($serv){
        global $config_file;
        //设定manager进程的名称
        $this->setProcessName('php-manager: '.APP_NAME.' ('.$config_file.')');
        $this->log(SERVER_NAME."[#manager] start");
    }

    /**
    * 管理进程结束时回调函数
    * @access public
    * @param \swoole_server $serv
    * @return void
    */
    public function onManagerStop($serv){
        $this->log(SERVER_NAME."[#manager] stop");
    }

    /**
    * 服务器关闭时回调函数
    * @access public
    * @param \swoole_server $serv
    * @return void
    */
    public function onShutdown($serv){
        exec('rm -rf '.SHM_PATH);
        $this->log(SERVER_NAME." shutdown");
        apply_action('on_shutdown',$this,$serv);
    }

    /**
    * 服务器启动时回调函数
    * @access public
    * @param \swoole_server $serv
    * @return void
    */
    public function onStart($serv){
    
    //设定master进程名称
    
         $this->setProcessName('php-master: ' . APP_NAME . ' host=' . $this->config['server']['host'] . ' port=' . $this->config['server']['port']);
        apply_action('server_start',$serv);
    }

    /**
    * work进程启动时回调函数
    * @access public
    * @param \swoole_server $server
    * @param int $worker_id
    * @return void
    */
    public function onWorkerStart($server,$worker_id){
        $this->worker_id = $worker_id;
        
        $this->loadPlugin();
        //初始化加载插件
        
        if ($worker_id >= $this->c('global.worker_num')){
      	    //如果work_id大于设定的work数量 则是task进程
            $this->setProcessName('php-task: '.APP_NAME.' #'.$worker_id);
            $this->log("php-task[#{$worker_id}] running on ".$this->c('server.host').":".$this->c('server.port'));
        }else{
            $this->worker_id = $worker_id;
            //设定work进程名称
            $this->setProcessName('php-worker: '.APP_NAME.' #'.$worker_id);
            $this->log("php-worker[#{$worker_id}] running on ".$this->c('server.host').":".$this->c('server.port'));
            apply_action('on_worker_start',$server,$worker_id);
        }
    }

    /**
    * work进程退出时执行
    * @access public
    * @param string $var
    * @return void
    */
    public function onWorkerStop($serv,$worker_id){
        $this->log("php-worker[#{$worker_id}] Stoped");
        exec('rm -rf '.SHM_PATH);
        while(\swoole_process::wait());
    }

    /**
    * 当work遇到错误时
    * @access public
    * @param swoole_server $serv
    * @return void
    */
    public function onWorkerError($serv,$worker_id,$worker_pid,$exit_code){
        em_log("php-worker[#{$worker_id}] Exit! pid:{$worker_pid} code:{$exit_code}, app_name:".APP_NAME.",traces: ".\Lib\Error::trace());
        $this->log("php-worker[#{$worker_id}] Exit! code:{$exit_code}");
    }

    /**
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
            
            //将data解包 解析出task_name 和参数
            //根据task_name在命名空间Task中实例化对应的类
            //在定时器中无法使用add_task()异步任务
            
            $task_name = $task_data['name'];
            $data = $task_data['data'];
            $params = '';
            $task = '\\Task\\'.ucwords($task_name);
            $task::run($data);
        }catch(\Exception $e){
            $this->log($e->getMessage());
        }
        // $serv->finish();
    }

    /**
    * 异步任务结束时回调函数
    * @access public
    * @param \swoole_server $var
    * @param int $task_id
    * @param string $data
    * @return void
    */
    public function onFinish($serv, $task_id, $data){
        echo 'task data:'.$data;
    }

    /**
    * 请求处理函数
    * @access public
    * @param swoole_request $rq
    * @param swoole_response $rs
    * @return void
    */
    public function onRequest(\swoole_http_request $rq,\swoole_http_response $rs){
        $this->rs = $rs;
        
        //初始化全局参数
        
        $_GET = $_POST = $_FILES = $_COOKIE = $_SERVER = $_REQUEST = $GLOBALS = [];
        if(!isset($rq->fd) || empty($rq->fd)){
            return false;
        }
        $this->fd = $rq->fd;
        $connection_info = $this->serv->connection_info($rq->fd);
        if($connection_info==false){
            return false;
        }
        try{
            isset($rq->cookie) && $_COOKIE = $rq->cookie;
            isset($rq->files) && $_FILES = $rq->files;
            isset($rq->get) && $_GET = $rq->get;
            isset($rq->post) && $_POST = $rq->post;
            if($this->isMulFormData(val($rq,'header')) && $this->isArrayPost($_POST)){
                $_POST = $this->post2Array($_POST);
            }
            $_REQUEST = array_merge($_GET,$_POST);
            $GLOBALS['rawContent'] = '';
            $connection_info && $GLOBALS['rawContent'] = $rq->rawContent();
            $header = $server = [];
            if(isset($rq->header)){
                foreach($rq->header as $key=>$val){
                    $header['HTTP_'.strtoupper(str_replace('-','_',$key))] = $val;
                }
            }
            if(isset($rq->server)){
                $server = array_change_key_case($rq->server,CASE_UPPER);
            }
            $_SERVER = array_merge($header,$server);
            unset($server,$header);
            $_SERVER['REMOTE_ADDR'] = val($_SERVER,'HTTP_X_FORWARDED_FOR',$_SERVER['REMOTE_ADDR']);
            $_SERVER['SERVER_SOFTWARE'] = SERVER_NAME;
            if(defined('DEBUG')){
                $query_str = val($_SERVER,'QUERY_STRING');
                $query_str && $query_str = '?'.$query_str;
                $this->log("new request from [#{$rs->fd}]:\"".$_SERVER['REQUEST_METHOD'].' '.$_SERVER['REQUEST_URI'].$query_str);
            }
            
            //将报文解析成数组
            
            apply_action('on_request');
            //实现请求响应的插件
            //回调到AppServer->start()
            call_user_func($this->_onRequest,$rq,$rs);
        }catch(\Expectation $e){
            $rs->status(400);
            $rs->end($e->getMessage());
        }finally{
        
            //资源回收
            $this->rs = null;
            $this->fs = null;
            $rq = null;
            $rs = null;
        }
    }

    /**
    * 处理请求回调函数
    * @access public
    * @param function $callback
    * @return void
    */
    public function setProcReqFun($callback){
        $this->_onRequest = $callback;
    }

    /**
     * 连接关闭时回调函数
     * @param \swoole_server $serv
     * @param int $fd
     * @param int $from_id
     * @return void
     */
    public function onClose($serv, $fd, $from_id){
        $this->clear();
        if(defined('DEBUG')) $this->log("client[#$fd@$from_id] close");
    }

    /**
    * 加载插件
    * @access private
    * @return void
    */
    private function loadPlugin(){
        $paths = array('global'=>FRAME_ROOT.'plugin/','app'=>DOCUMENT_ROOT.'Plugin/');
        foreach($paths as $type => $plugin_path){
            if(!is_dir($plugin_path)){
                continue;
            }
            $odir = opendir($plugin_path);
            while($file = readdir($odir)){
                if($file{0}!='.' && ($path=$plugin_path.$file.'/') && ($plugin_file=$path.$file.'.php') && is_file($plugin_file) && (is_file($path.'enabled') || $type == 'global')){
                 	//加载框架和应用下的插件
                    include $plugin_file;
                }
            }
            closedir($odir);
        }
    }

   
    
    /**
    * 发送内容
    * @access public
    * @param \swoole_server $serv
    * @param int $fd
    * @param string $respData
    * @param int $code
    * @return void
    */
    public function response($respData, $code = 200){
        if(empty($this->rs)){
            return false;
        }
        $connection_info = $this->serv->connection_info($this->fd);
        if($connection_info==false){
            return false;
        }
        try{
            $this->c('gzip_level') && $this->rs->gzip($this->c('gzip_level'));
            $this->rs->status($code);
            $this->c('server.keepalive') && $this->setHeader('Connection','keep-alive');
            $strlen = strlen($respData);
            if($strlen > 1024*1024*2){
                $this->setHeader('Content-Length',$strlen);
                $p=0;
                $s=2000000;
                while($data = substr($respData, $p++*$s,$s)){
                    $this->rs->write($data);
                }
                $this->rs->end();
            }else{
                $this->rs->end((string)$respData);
            }
            return true;
        }catch(\Exception $e){
            $this->rs->status(500);
            $rs->end($e->getMessage());
            return true;
        }finally{
            $this->rs = null;
        }
        return true;
    }


       /**
     * Fatal Error的捕获
     * @access public
     * @return void
     */
    public function handleFatal(){
        $error = error_get_last();
        $data = '';
        if(($data = ob_get_contents())!==false){
            ob_end_clean();
        }
        /**
        * 如果脚本退出时，检测到has_process为true，说明是子进程退出
        * 此时直接写到日志，而不是向客户端发送数据
        */
        if($this->has_process){
            if(!empty($data) || !empty($error)){
                $msg = $data.$error['message'];
                $errorMsg = "{$error['message']} ({$error['file']}:{$error['line']})";
                global $php;
                $sql = $php->db->getLastSql();
                em_log('child process fatal error : '.$errorMsg.\Lib\Error::trace().($sql ? ' [SQL]:'.$sql : ''));
            }
            return;
        }
        if (empty($error) || !isset($error['type'])){
            $new_data = json_decode($data,true);
            if(gettype($new_data)=='array'){
                $new_data['_worker_info'] = 'worker restarted';
                $data = json_encode($new_data,JSON_UNESCAPED_UNICODE);
            }else{
                $data .= '<font color="red">worker restarted</font>';
            }
            $this->response($data);
            return;
        }else{
            $error['file'] = str_replace('.php','',$error['file']);
            $errorMsg = "{$error['message']} (".str_replace([SHM_PATH.'tpls/',SHM_PATH.'widget/tpl/',APP_PATH,FRAME_ROOT,DOCUMENT_ROOT],['tpl:','widget_tpl:','app:','root:','app:'],$error['file']).":{$error['line']})";
            $message = \Lib\Error::info("Server fatal error", $errorMsg);
            em_log('Server fatal error : '.$errorMsg.\Lib\Error::trace().' URL:'.cur_url());
            if(defined('DEBUG')){
                $this->response($message);
            }else{
                $this->response(\Lib\Error::info("", '页面发生错误'),200);
            }
        }
    }
}

```
