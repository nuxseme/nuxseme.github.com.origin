---
title: travis-hexo
categories:
  - Hexo
---

### 持续集成

>在用hexo 或者 jekyll 搭建个人博客。在平时会不定期发布文章，然后重新生成文档部署到线上。这就是一个典型的不断开发，不断测试，不断部署的例子。持续集成简单讲，就是一个不断开发，不断测试，不断部署的流程，为了方便，就出现了自动持续集成

### travis-ci 

>免费开源，结合github非常方便。
1 travis 会在收到你的push之后自动建立一个虚拟环境，执行.travis.yml指令
2 你的github项目中包含正确配置的.travis.yml文件
3 在travis.org中，将你的github项目同步过去，并开启配置

<!--more-->
### 技巧和坑

> 1 GH\_TOKEN  
  拿到github Personal access tokens,填写时注意空格，GH_TOKEN 不能写成GH-TOKEN  
  2 GH_REF   
  GH\_REF 全局变量，冒号之后需要空格，yml强格式  
  3 theme/  
  确保主题上传到了git中，否则会出现空白，hexo g 不会生成js css  
  多看travis  job log  


### .travis.yml

	language: node_js #指定语言
	node_js: stable #指定版本

	install:
	  - npm install #安装npm 包管理
	  - npm install hexo-cli -g #安装hexo

	notifications:
	  email:
	    - nuxseme@gmail.com
	  on_success: always
	  on_failure: always
	  
	script:
	  - hexo -v #查看版本信息确认是否安装成功
	  - hexo clean # 清除public文件
	  - hexo g # 重新生成public

	after_script:
	  - cd ./public #进入生成的public目录
	  - git init #初始化git
	  - git config  user.name "nuxseme" #设定git配置
	  - git config  user.email "nuxseme@gmail.com"
	  - git add . #加入暂存区
	  - git commit -m "Auto deploy from Travis-CI " # 提交生成版本
	  - git push --force  "https://${GH_TOKEN}@${GH_REF}" master:gh-pages #推送到远端 master是指刚刚生成的public的分支，gh-pages是指远端GH_REF的分支，这个分支是用来发布的  ${GH_TOKEN} 来自travis setting设置的全局变量

	branches:
	  only:
	    - master
	env:
	 global:
	   - GH_REF: github.com/nuxseme/travis-ci.com.git #全局配置


