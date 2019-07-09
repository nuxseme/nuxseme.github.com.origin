---
title: php-文件锁
date: 2019-07-09 09:56:17
categories: php
---

#### 语法

flock(file,lock,block)

	
	参数	描述
	file	必需。规定要锁定或释放的已打开的文件。
 	lock	必需。规定要使用哪种锁定类型。
	block	可选。若设置为 1 或 true，则当进行锁定时阻挡其他进程。
	
#### 说明
flock() 操作的 file 必须是一个已经打开的文件指针。

lock 参数可以是以下值之一：

要取得共享锁定（读取的程序），将 lock 设为 LOCK_SH（PHP 4.0.1 以前的版本设置为 1）。
要取得独占锁定（写入的程序），将 lock 设为 LOCK_EX（PHP 4.0.1 以前的版本中设置为 2）。
要释放锁定（无论共享或独占），将 lock 设为 LOCK_UN（PHP 4.0.1 以前的版本中设置为 3）。
如果不希望 flock() 在锁定时堵塞，则给 lock 加上 LOCK_NB（PHP 4.0.1 以前的版本中设置为 4）。

#### 实例

	1
	$file = './log';
	$fp = fopen($file,'a');
	
	if(flock($fp, LOCK_EX)){ //排他锁
	    echo fwrite($fp, ' world1');
	}
	sleep(30);//hold住不放
	flock($fp,LOCK_UN);#要释放
	fclose($fp);
	echo '30s之后释放锁';



	2
	$file = './log';
	$fp = fopen($file,'a');
	
	if(flock($fp, LOCK_EX)){ //排他锁
	    echo '获取到排它锁';
	    echo fwrite($fp, ' world2');
	}else{
	    echo '未获取到排它锁';
	}
	flock($fp,LOCK_UN);#释放
	fclose($fp);	
	echo 'end';


> 这里主要想测试flock的检测是不是阻塞的，运行实例1，写入数据并持有排它锁，未释放。运行实例2，无任何输出，等30s之后，实例1释放排它锁，实例2获取到排它锁。flock是阻塞的，会一直等到锁的释放。所以在日志中央服务器，多进程消耗日志数据时，不会因为锁的逻辑丢失数据包。

ps:锁获取的顺序会影响数据的写入顺序吗？日志进程的常驻维护，如何确保不中断？（crontab 定时检测+告警 端口活动检测）