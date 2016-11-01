---
title: git创建项目并推送到git仓库
categories:
  - null
date: 2016-10-31 23:01:40
---


	$mkdir fittest #建立目录
	$echo hello > hello #初始化目录 假定我们需要对当前目录版本控制
	$git init
	Initialized empty Git repository in /opt/project/gittest/.git/
	[osx: ~/opt/project/gittest ]$ ll
	total 4
	drwxr-xr-x 4 hyd wheel 136 10 31 22:43 .
	drwxr-xr-x 7 hyd wheel 238 10 31 22:43 ..
	drwxr-xr-x 9 hyd wheel 306 10 31 22:43 .git
	-rw-r--r-- 1 hyd wheel   6 10 31 22:43 hello
	[osx: ~/opt/project/gittest ]$ git remote -v
	[osx: ~/opt/project/gittest ]$ git remote add origin git@github.com:nuxseme/gittest.git
	[osx: ~/opt/project/gittest ]$ git remote -v
	origin	git@github.com:nuxseme/gittest.git (fetch)
	origin	git@github.com:nuxseme/gittest.git (push)
	
	[osx: ~/opt/project/gittest ]$ git status
	On branch master

	Initial commit

	Untracked files:
	(use "git add <file>..." to include in what will be committed)

	hello

	nothing added to commit but untracked files present (use "git add" to track)
	[osx: ~/opt/project/gittest ]$ git add .
	[osx: ~/opt/project/gittest ]$ git commit -m 'init'
	[master (root-commit) 76ffc94] init
	1 file changed, 1 insertion(+)
	create mode 100644 hello
	[osx: ~/opt/project/gittest ]$ git status
	On branch master
	nothing to commit, working directory clean
	[osx: ~/opt/project/gittest ]$ git push -u origin master
	ERROR: Repository not found.
	fatal: Could not read from remote repository.

	Please make sure you have the correct access rights
	and the repository exists.
	
	#这里需要先在github上建好仓库才能推送
	[osx: ~/opt/project/gittest ]$ git status
	On branch master
	Your branch is up-to-date with 'origin/master'.
	nothing to commit, working directory clean
	
	
