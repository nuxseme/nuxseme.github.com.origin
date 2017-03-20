---
title: pngquant
date: 2017-03-17 12:04:03
categories: php
---

### 概述

> 官网：[https://pngquant.org](https://pngquant.org)  
> git:[https://github.com/pornel/pngquant.git](https://github.com/pornel/pngquant.git)  
> 源码安装流程[https://pngquant.org/install.html](https://pngquant.org/install.html)  
> pngquant 开源优秀的png压缩方案


### 安装

	$git clone --recursive https://github.com/pornel/pngquant.git
    $cd pngquant
	$./configure --prefix=dir //指定安装目录  默认 /usr/local/bin
	$make && make install
	$./pngquant --version
	2.9.0 (March 2017)
	$./pngquant -h
	pngquant, 2.9.0 (March 2017), by Kornel Lesinski, Greg Roelofs.
	   Compiled with no support for color profiles. Using libpng 1.5.13.
	
	WARNING: Your version of libpng is old and has buggy support for custom chunks.
	Please recompile pngquant with the current version of libpng (1.6 or later).
	
	usage:  pngquant [options] [ncolors] -- pngfile [pngfile ...]
	        pngquant [options] [ncolors] - >stdout <stdin
	
	options:
	  --force           overwrite existing output files (synonym: -f)
	  --skip-if-larger  only save converted files if they're smaller than original
	  --output file     destination file path to use instead of --ext (synonym: -o)
	  --ext new.png     set custom suffix/extension for output filenames
	  --quality min-max don't save below min, use fewer colors below max (0-100)
	  --speed N         speed/quality trade-off. 1=slow, 3=default, 11=fast & rough
	  --nofs            disable Floyd-Steinberg dithering
	  --posterize N     output lower-precision color (e.g. for ARGB4444 output)
	  --strip           remove optional metadata (default on Mac)
	  --verbose         print status messages (synonym: -v)
	
	Quantizes one or more 32-bit RGBA PNGs to 8-bit (or smaller) RGBA-palette.
	The output filename is the same as the input name except that
	it ends in "-fs8.png", "-or8.png" or your custom extension (unless the
	input is stdin, in which case the quantized image will go to stdout).
	If you pass the special output path "-" and a single input file, that file
	will be processed and the quantized image will go to stdout.
	The default behavior if the output file exists is to skip the conversion;
	use --force to overwrite. See man page for full list of options.

<!--more-->
### 使用

	$pngquant --quality=50-90  -<cat.png >cat1.png //215K 压缩到 57K 
	$ll
	total 340
	drwxr-xr-x.  2 root root     50 Mar 17 14:38 .
	drwxr-xr-x. 10 root root   4096 Mar 17 12:45 ..  
	-rw-r--r--.  1 root root  57K Mar 17 12:21 cat1.png
	-rw-r--r--.  1 root root 215K Mar 17 12:14 cat.png

### Using pngquant in PHP

> php 采用shell_exec()调用pngquant来压缩 能否将pngquant写入扩展？？

	<?php

	/**
	 * Optimizes PNG file with pngquant 1.8 or later (reduces file size of 24-bit/32-bit PNG images).
	 *
	 * You need to install pngquant 1.8 on the server (ancient version 1.0 won't work).
	 * There's package for Debian/Ubuntu and RPM for other distributions on http://pngquant.org
	 *
	 * @param $path_to_png_file string - path to any PNG file, e.g. $_FILE['file']['tmp_name']
	 * @param $max_quality int - conversion quality, useful values from 60 to 100 (smaller number = smaller file)
	 * @return string - content of PNG file after conversion
	 */
	function compress_png($path_to_png_file, $max_quality = 90)
	{
    if (!file_exists($path_to_png_file)) {
        throw new Exception("File does not exist: $path_to_png_file");
    }

    // guarantee that quality won't be worse than that.
    $min_quality = 60;

    // '-' makes it use stdout, required to save to $compressed_png_content variable
    // '<' makes it read from the given file path
    // escapeshellarg() makes this safe to use with any path
    $compressed_png_content = shell_exec("pngquant --quality=$min_quality-$max_quality - < ".escapeshellarg(    $path_to_png_file));

    if (!$compressed_png_content) {
        throw new Exception("Conversion to compressed PNG failed. Is pngquant 1.8+ installed on the server?");
    }

    return $compressed_png_content;
	}

	//example
	$read_from_path = $_FILE['file']['tmp_name'];
	$save_to_path = "uploads/compressed_file.png";

	$compressed_png_content = compress_png($read_from_path);
	file_put_contents($save_to_path, $compressed_png_content);

	// you don't need move_uploaded_file(). file_put_contents() will do that instead.

