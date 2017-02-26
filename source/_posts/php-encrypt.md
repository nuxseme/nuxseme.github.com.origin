---
title: encrypt
categories: php
date: 2017-02-22 09:53:11
---



#### 内置加密函数

	$plaintext = '123456';
	$ciphertext = password_hash($plaintext,PASSWORD_DEFAULT);
	echo $ciphertext,PHP_EOL;
	
	$ciphertext = '$2y$10$0nEIpqfr5pfBlKeCt6ak1O33X.BFURM9WZ8QtAazz83xwLd0ggH8.';
	$verify = password_verify($plaintext,$ciphertext);
	echo $verify,PHP_EOL;//true
	
	$password_info = password_get_info($ciphertext);
	print_r($password_info)；

 
> 1 默认采用 bcrypt 加密  
  2 自动添加盐值，同一明文，hash之密文不相同。建议采用默认随机盐值  
  3 password_verify 自动解析盐值，校验密文  
  在实际运用中 密文可能需要跟其他系统配合，其他语言框架不支持password_hash,需要根据实际情况而定
 

