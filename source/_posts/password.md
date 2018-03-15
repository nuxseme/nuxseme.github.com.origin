---
title: password
categories: php
date: 2017-06-11 20:27:41
---


### bcrypt
> 目前最安全的hash算法，bcrypt。bcrypt自动加盐，意味着同一明文经过bcrypt得到的密文不同。bcrypt故意设计的很慢，会经过设定的work factor反复处理。目前默认是10，如果计算速度更快了，只需要提高work factor的值即可

### password_hash

	password_hash($password, $algo, $options = null)
	//加密明文  加密方式，默认bcrypt 其他参数设置
	
### password_verify

	password_verify($password, $hash)
	//明文  hash值  不可逆
	
### password_needs_rehash

	password_needs_rehash($hash, $algo, $options = null)
	//hash 算法 操作参数
	//提升了工作参数 同一明文就需要重新计算hash
	
### password_get_info
	Array
	(
    [algo] => 1
    [algoName] => bcrypt
    [options] => Array
        (
            [cost] => 10
        )

	)
	
	