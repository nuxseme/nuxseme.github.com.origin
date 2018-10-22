---
title: git
date: 2018-06-21 22:42:13
categories: git
---

### 常用命令

	$git commit --amend #修改上次提交记录
	$git stash #暂存 将暂存区和工作区未提交文件保存下来可以切换分支
	$git stash pop|list #暂存应用
	$git diff [--cached|HEAD] #比较work 和 stash的差异 比较stash 和 repository
	$git format-patch
	$git rm --cached #删除已经提交的文件
	$git config [--global|--system] core.quotepath false #中文乱码  当前项目  当前用户  当前操作系统
	