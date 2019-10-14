---
title: php延迟静态绑定
categories: php
date: 2018-03-27 10:18:29
---

> 延迟静态绑定指使用static关键词调用方法，如果如遇到self static  parent等关键词调用者需要被转移到最近的方法
> 使用场景较为局限，php 静态方法比较少，静态方法+继承使用的场景就更少了


	class A {

    public static function test()
    {
       static::who();

    }

    public static function who()
    {
        echo __CLASS__,PHP_EOL;
    }

	}
	
<!--more-->
	class B extends A{
	
	    public static function test()
	    {
	        parent::test();
	    }
	
	    public static function who()
	    {
	        echo __CLASS__,PHP_EOL;
	    }
	}
	
	class C extends B {
	
	    public static function run()
	    {
	        A::test();
	        B::test();
	        parent::test();
	        self::test();
	        static::test();
	    }
	
	    public static function who()
	    {
	        echo __CLASS__,PHP_EOL;
	    }
	
	}
	
	C::run();
	
	A
	B
	C
	C
	C
	
	class C extends B {
	
	    public static function run()
	    {
	        A::test();
	        B::test();
	        parent::test();
	        self::test();
	        static::test();
	    }
	
	    //public static function who()
	    //{
	    //    echo __CLASS__,PHP_EOL;
	    //}
	
	}
	
	C::run();
	
	A
	B
	B
	B
	B