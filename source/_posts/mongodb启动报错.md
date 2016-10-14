---
title: mongodb启动报错
categories:
  - MongoDB
date: 2016-06-29 23:24:51
---


### 报错信息
>child process failed, exited with error number 100

### 查看mongodbp配置

	$cat mongodb.cnf 
	#configuration Options for MongoDB
	#
	# For More Information, Consider:
	# - Configuration Parameters: http://www.mongodb.org/display/DOCS/Command+Line+Parameters
	# - File Based Configuration: http://www.mongodb.org/display/DOCS/File+Based+Configuration
	dbpath = /home/mongo/db/
	logpath = /home/mongo/log/m.log
	logappend = true
	#绑定监听地址
	bind_ip = 127.0.0.1
	port = 27017
	fork = true
	#auth = true
	noauth = true
	directoryperdb = true
	journal = true
<!--more-->
### 查看日志

>  Please make at least 3379MB available in /home/mongo/db/journal or use --smallfiles
```
2016-06-22T17:09:59.814+0800 I CONTROL  ***** SERVER RESTARTED *****
2016-06-22T17:09:59.932+0800 I STORAGE  [initandlisten] 
2016-06-22T17:09:59.932+0800 I STORAGE  [initandlisten] ** WARNING: Readahead for /home/mongo/db/ is set to 4096KB
2016-06-22T17:09:59.932+0800 I STORAGE  [initandlisten] **          We suggest setting it to 256KB (512 sectors) or less
2016-06-22T17:09:59.932+0800 I STORAGE  [initandlisten] **          http://dochub.mongodb.org/core/readahead
2016-06-22T17:09:59.933+0800 I JOURNAL  [initandlisten] journal dir=/home/mongo/db/journal
2016-06-22T17:09:59.933+0800 I JOURNAL  [initandlisten] recover : no journal files present, no recovery needed
2016-06-22T17:09:59.933+0800 I JOURNAL  [initandlisten] 
2016-06-22T17:09:59.933+0800 E JOURNAL  [initandlisten] Insufficient free space for journal files
2016-06-22T17:09:59.933+0800 I JOURNAL  [initandlisten] Please make at least 3379MB available in /home/mongo/db/journal or use --smallfiles
2016-06-22T17:09:59.933+0800 I JOURNAL  [initandlisten] 
2016-06-22T17:09:59.963+0800 I STORAGE  [initandlisten] exception in initAndListen: 15926 Insufficient free space for journals, terminating
2016-06-22T17:09:59.963+0800 I CONTROL  [initandlisten] now exiting
2016-06-22T17:09:59.963+0800 I NETWORK  [initandlisten] shutdown: going to close listening sockets...
2016-06-22T17:09:59.963+0800 I NETWORK  [initandlisten] shutdown: going to flush diaglog...
2016-06-22T17:09:59.963+0800 I NETWORK  [initandlisten] shutdown: going to close sockets...
2016-06-22T17:09:59.963+0800 I STORAGE  [initandlisten] shutdown: waiting for fs preallocator...
2016-06-22T17:09:59.963+0800 I STORAGE  [initandlisten] shutdown: final commit...
2016-06-22T17:09:59.963+0800 I STORAGE  [initandlisten] shutdown: closing all files...
2016-06-22T17:09:59.963+0800 I STORAGE  [initandlisten] closeAllFiles() finished
2016-06-22T17:09:59.963+0800 I CONTROL  [initandlisten] dbexit:  rc: 100
```

### 除去启动时warning
	
	MongoDB shell version: 3.2.10
	connecting to: test
	Welcome to the MongoDB shell.
	For interactive help, type "help".
	For more comprehensive documentation, see
		http://docs.mongodb.org/
	Questions? Try the support group
		http://groups.google.com/group/mongodb-user
	Server has startup warnings: 
	2016-10-15T01:18:21.530+0800 I STORAGE  [initandlisten] 
	2016-10-15T01:18:21.530+0800 I STORAGE  [initandlisten] ** WARNING: Readahead for /var/mongodb/db/ is set to 4096KB
	2016-10-15T01:18:21.530+0800 I STORAGE  [initandlisten] **          We suggest setting it to 256KB (512 sectors) or less
	2016-10-15T01:18:21.530+0800 I STORAGE  [initandlisten] **          http://dochub.mongodb.org/core/readahead
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] ** WARNING: You are running this process as the root user, which is not recommended.
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] 
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] 
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] 
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
	2016-10-15T01:18:21.587+0800 I CONTROL  [initandlisten]
	
	//除去磁盘读取块大小设置warning
	df -h
	Filesystem               Size  Used Avail Use% Mounted on
	/dev/mapper/centos-root   38G  4.8G   33G  13% /
	devtmpfs                 479M     0  479M   0% /dev
	tmpfs                    489M     0  489M   0% /dev/shm
	tmpfs                    489M  6.8M  483M   2% /run
	tmpfs                    489M     0  489M   0% /sys/fs/cgroup
	/dev/sda1                497M  123M  375M  25% /boot
	tmpfs                     98M     0   98M   0% /run/user/0
	.host:/                  128G   29G  100G  22% /mnt/hgfs
	
	blockdev --report
	RO    RA   SSZ   BSZ   StartSec            Size   Device
	rw  8192   512  4096          0     42949672960   /dev/sda
	rw  8192   512   512       2048       524288000   /dev/sda1
	rw  8192   512  4096    1026048     42424336384   /dev/sda2
	rw   256  2048  2048          0        74151936   /dev/sr0
	rw  8192   512   512          0     40227569664   /dev/dm-0
	rw  8192   512  4096          0      2147483648   /dev/dm-1
	
	blockdev --setra 256 /dev/mapper/centos-root
	
	blockdev --report
	RO    RA   SSZ   BSZ   StartSec            Size   Device
	rw  8192   512  4096          0     42949672960   /dev/sda
	rw  8192   512   512       2048       524288000   /dev/sda1
	rw  8192   512  4096    1026048     42424336384   /dev/sda2
	rw   256  2048  2048          0        74151936   /dev/sr0
	rw  8192   512   512          0     40227569664   /dev/dm-0
	rw  8192   512  4096          0      2147483648   /dev/dm-1
	
	echo never > /sys/kernel/mm/transparent_hugepage/enabled
	echo never > /sys/kernel/mm/transparent_hugepage/defrag

	#效果

	[ ~ ]$ mongo
	MongoDB shell version: 3.2.10
	connecting to: test
	Server has startup warnings: 
	2016-10-15T01:32:45.014+0800 I CONTROL  [initandlisten] ** WARNING: You are running this process as the root user, which is not recommended.
	2016-10-15T01:32:45.014+0800 I CONTROL  [initandlisten] 
	>

### 总结
> 前面mongodb启动错误,通常直接google百度,发现网上的答案往往跟自己的不太符合,这个时候最有效的方式就是直接查看日志。安装时，别忘记根据配置建立db和log目录