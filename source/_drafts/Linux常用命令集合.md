---
title: Linux常用命令集合
categories:
  - Linux
date:
---

	apropos desc
	*apropos 命令反查


	whatis command
	whatis 简介命令whereis filename
	whereis  查找应用程序文件/文件夹 在指定目录搜索文件

	updatedb 更新文件数据库locate filename
	locate 查找文件  需要维护数据库

	find [dir|默认当前文件夹] -name filename
	find 在指定文件目录中查找文件

	mkdir -p dir/dir 递归创建文件夹

	>  输出重定向 建立 覆盖
	>> 建立 追加
	命令替换  ``  $(...)
	umask 077 删除除用户以外的其他人的访问权限
	temp=${TMPDIR-/tmp}/mytemp.$$  进程pid后缀 产生临时文件
	trap ' rm -f $temp' EXIT 完成时删除临时文件

	type command  显示命令的来源

	df  disk free 显示磁盘使用情况
	du  disk usage 磁盘用量 -sh  摘要