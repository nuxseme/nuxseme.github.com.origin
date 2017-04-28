---
title: 简单(正向)冒泡排序-BubbleSort
date: 2017-04-27 09:45:13
categories:
---


### 视觉直观图

![冒泡排序](/img/Bubble-Sort.gif)
![冒泡排序](http://img.blog.csdn.net/20150811164218318)
![冒泡排序](http://img.blog.csdn.net/20150606135803929)

<!--more-->
### 原理

1 对无序的队列由开始到结尾两两比较，小的靠前，大的靠后  
2 外层遍历次数n,内层遍历次数n-1  
3 每次排序之后，最大的数值放到了尾部  

### 优化

- 优化外层遍历次数，倒数第二次排序，队列已经稳定  
- 添加是否exchange标识位，在一趟遍历中未发生任何交换表明结果已经稳定
- 优化结束标识位，每次遍历最大的数值已经靠后，不需要再次比较

### 代码
	<?php
	/**
	 * @link www.github.com/nuxseme/php-practice
	 * @copyright Copyright (c) 2017 nuxseme
	 * @license MIT
	 */
	//版本一  简单正向冒泡
	function bubbleSort(&$array){
    $length = count($array);
    $times = 0;
    for ($i = 0; $i <= $length-1; $i++) {
        for($j = 0; $j <= $length - 2; $j++) {
            $times ++;
            $temp = $array[$j];
            if($array[$j] > $array[$j+1]){
                $array[$j] = $array[$j+1];
                $array[$j+1] = $temp;
            }
        }
    }
    echo $times,PHP_EOL;
	}
	$origin_array = [2,6,4,9,8,5,1,3,7];
	bubbleSort($origin_array);//72
	//print_r($origin_array);
	$origin_array = [1,2,3,4,5,6,7,8,9];
	bubbleSort($origin_array);//72
	
	//版本二 优化外层次数
	function bubbleSort1(&$array){
    $length = count($array);
    $times = 0;
    for ($i = 0; $i <= $length-2; $i++) {
        for($j = 0; $j <= $length - 2; $j++) {
            $times ++;
            $temp = $array[$j];
            if($array[$j] > $array[$j+1]){
                $array[$j] = $array[$j+1];
                $array[$j+1] = $temp;
            }
        }
    }
    echo $times,PHP_EOL;
	}
	$origin_array = [2,6,4,9,8,5,1,3,7];
	bubbleSort1($origin_array);//64
	//print_r($origin_array);
	$origin_array = [1,2,3,4,5,6,7,8,9];
	bubbleSort1($origin_array);//64
	//print_r($origin_array);
	
	//版本三 exchange标识位  一趟排序不会发生交换表明排序已经趋于稳定
	function bubbleSort2(&$array){
    $length = count($array);
    $times = 0;
    for ($i = 0; $i <= $length-2; $i++) {
        $exchange = false;
        for($j = 0; $j <= $length - 2; $j++) {
            $temp = $array[$j];
            $times++;
            if($array[$j] > $array[$j+1]){
                $array[$j] = $array[$j+1];
                $array[$j+1] = $temp;
                $exchange = true;
            }
        }
        if(!$exchange) break;
    }
    echo $times,PHP_EOL;
	}
	$origin_array = [2,6,4,9,8,5,1,3,7];
	bubbleSort2($origin_array);//56
	//print_r($origin_array);
	$origin_array = [1,2,3,4,5,6,7,8,9];
	bubbleSort2($origin_array);//8
	//print_r($origin_array);
	
	
	//版本四  exchange标识位 + 结束标识位
	
	function bubbleSort3(&$array){
    $length = count($array);
    $times = 0;
    for ($i = 0; $i <= $length-2; $i++) {
        $exchange = false;
        for($j = 0; $j <= $length - 2 - $i; $j++) {
            $temp = $array[$j];
            $times++;
            if($array[$j] > $array[$j+1]){
                $array[$j] = $array[$j+1];
                $array[$j+1] = $temp;
                $exchange = true;
            }
        }
        if(!$exchange) break;
    }
    echo $times,PHP_EOL;
	}
	$origin_array = [2,6,4,9,8,5,1,3,7];
	bubbleSort3($origin_array);//35
	//print_r($origin_array);
	$origin_array = [1,2,3,4,5,6,7,8,9];
	bubbleSort3($origin_array);//8
	//print_r($origin_array);