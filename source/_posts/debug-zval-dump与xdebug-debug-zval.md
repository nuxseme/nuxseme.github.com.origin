---
title: debug_zval_dump与xdebug_debug_zval
categories: php
date: 2017-04-22 14:16:33
---


	<?php
	$a = 'hello world';
	$b = $a;
	$c = &$a;
	xdebug_debug_zval('a');
	xdebug_debug_zval('b');
	xdebug_debug_zval('c');

	$php -m|grep xdebug
	xdebug

	$php zval.php 
	a: (refcount=2, is_ref=1)='hello world'
	b: (refcount=0, is_ref=0)='hello world'
	c: (refcount=2, is_ref=1)='hello world'
	string(11) "hello world" refcount(1)
	string(11) "hello world" refcount(1)
	string(11) "hello world" refcount(1)
