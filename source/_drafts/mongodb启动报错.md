---
title: mongodb启动报错
categories:
  - MongoDB
date:
---

### 报错信息
>child process failed, exited with error number 100

### 查看mongodbp配置

>  
```
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
```
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

### 总结
> 前面mongodb启动错误,通常直接google百度,发现网上的答案往往跟自己的不太符合,这个时候最有效的方式就是直接查看日志
