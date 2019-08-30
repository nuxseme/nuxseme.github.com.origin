---
title: php-opcache
categories: php
date: 2019-08-07 14:26:34
---



#### php执行流程

> load php脚本-> Scanning(语法解析)-> Parsing（词法解析）-> Compilation（编译成opcache）-> Execution(执行opcache)->END


优化点在于减少语法和词法解析步骤

1. opcache的设置文件数一定要大于项目的所有文件数，不然没缓存的文件不断替换 
2. 文件更新，比如项目发版的时候opcache要不要清理掉
	* 如果清理掉，开始瞬间的请求都拿不到缓存的opcache,会导致服务器瞬间负载很高。同一个文件，多个请求会发生互斥锁
	* 不清理，采用opcache每次自动检查更新，这样会导致代码更新了，可能不会马上生效
	
	每次发版，更新到文件数量不会很多，而且会在秒级别的更新opcache是可以接受的。最佳方案应该是设定opcache自动更新，而非更新代码之后刷新opcache
3. opcache 要注意设定的缓存大小，达到最大缓存，opcache不断刷新