---
title: swoole_reload
date: 2019-07-26 11:16:59
categories: swoole
---

> 常驻内存，在workStart之前包含的文件无法reload
> 

####  server


	<?php
	$server = new \Swoole\Server('127.0.0.1', 10002, SWOOLE_PROCESS, SWOOLE_SOCK_TCP);
	
	$server->set([
	    'worker_num' => 1,    //worker process num
	    'dispatch_mode' => 1,
	]);
	
	$server->on('connect', function ($server, $fd){
	    echo "Client:Connect.\n";
	});
	$server->on('WorkerStart', function(\Swoole\Server $server, $workerId) {
	    if($workerId == 0) {
	        PHP_OS == 'Linux' && swoole_set_process_name( 'swoole server manager');
	    }else{
	        PHP_OS == 'Linux' && swoole_set_process_name( 'swoole worker '.$workerId);
	    }
	    echo $workerId;
	    echo '这个文件不能加载reload';
	    var_dump(get_included_files()); //此数组中的文件表示进程启动前就加载了，所以无法reload
	});

<!--more-->

	$server->on('receive', function (\Swoole\Server $server, $fd, $reactor_id, $data) {
	    echo 'data from client ',$data,PHP_EOL;
	    if($data == 'reload') {
	        $server->reload();
	    }else{
	
	        include_once 'change.php';
	
	        $server->send($fd, 'data  from  server: reactor_id'.$reactor_id.' data '.$data);
	        $server->close($fd);
	    }
	});
	$server->on('close', function ($server, $fd) {
	    echo "Client: Close.\n";
	});
	$server->start();
	
	
####  client


	<?php
	
	$msg = $argv[1] ?? 'hello';
	
	//
	//$fp = stream_socket_client("tcp://127.0.0.1:10001", $errno, $errstr) or die("error: $errstr\n");
	//fwrite($fp, pack('N', strlen($msg)) . $msg);
	//sleep(1);
	////将显示 hello world\n
	//$data = fread($fp, 10001);
	//var_dump(substr($data, 4, unpack('N', substr($data, 0, 4))[1]));
	//fclose($fp);
	
	
	
	$client = new Swoole\Client(SWOOLE_SOCK_TCP, SWOOLE_SOCK_ASYNC);
	$client->on("connect", function(Swoole\Client $cli)use($msg) {
	    $cli->send($msg);
	});
	$client->on("receive", function(Swoole\Client $cli, $data){
	    echo "Receive: $data",PHP_EOL;
	    //$cli->send(str_repeat('A', 100)."\n");
	    //sleep(1);
	});
	$client->on("error", function(Swoole\Client $cli){
	    echo "error\n";
	});
	$client->on("close", function(Swoole\Client $cli){
	    echo "Connection close\n";
	});
	$client->connect('127.0.0.1', 10002);

