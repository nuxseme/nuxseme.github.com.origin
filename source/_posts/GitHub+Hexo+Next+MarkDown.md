---
title: GitHub+Hexo+Next+MarkDown
categories:
  - Hexo
date: 2016-06-25 10:28:21
---
#### 导语
***
> 个人搭建博客，想要自己从前端到后台都实现，同时完善后台框架，周期太长。想找个记录笔记的地方，锻炼下文档的能力，博客的数据量不大，不需要用到数据库，恰好看到Jekyll|Hexo+GitHub Pages静态化存到github上，不错的选择。

#### 前言
***
#### Jekyll && Hexo

> Jekyll是一款源码文档到成品的转化引擎，通常我们在网页上看到的静态页面包含css和js的,除去文档自身的css、js,头部，尾部，侧边栏，评论系统等等都需要配置。jekyll引擎做了这样一件事情，我们预先配置好头尾，侧边栏，分类等等,这些文件按照一定的规则存放在不同的文件目录。jekyll和Hexo就帮我们将css,js和其他配置生成一个静态的框架，我们在这个静态的框架下写文档就好，最终我们的关注点就只放在文档自身.github.io内置jekyll引擎，所以我们将写好的文档源码连同项目配置上传到github规范的仓库就部署好了。Hexo则需要先生成好项目，上传部署。jekyll和Hexo都可以搭配很多开源优秀的主题使用，我自己用的是Hexo+Next.Mist,只测试了下Jekyll的使用.

<!--more-->
	
#### MarkDown

> markdown 教程比较多也很完善，这里写上它完全是因为喜爱。小而美。学习很简单，找篇别人的markdown源文件对应者预览，想要实现什么效果对应看源码格式就好，多写几次就能满足基本的文档需要。专注文档本身，而非花里胡哨没必要的东西。
	
#### Hexo的安装配置
***

##### 1 node 安装  
网址 <https://nodejs.org> 包含mac、win、linux等版本选择自己对应的下载安装

	$node -v  
	$v6.2.2
	$npm -v
	$3.9.5	
##### 2 hexo 安装
	
	$sudo npm install -g hexo-cli
	$sudo npm install hexo-server
这里通常会出错，对应错误google即可，建议加上sudo，可能会出现目录权限不够

	$hexo init #进入自定义的项目目录之后，hexo init会初始化 并clone git上的默认主题
	$hexo s #hexo server 出现这个说明启动成功，启动失败可以用hexo server --debug这
	个时候会在项目跟目录下生成debug.log 查找原因即可
	INFO  Start processing
	INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
	
通常我们也会将git的已有的项目拉取下来，这个时候小心用hexo init 会重新初始化。这个时候需要执行下
	
	$npm install
	$hexo s
	$hexo s --draft #预览草稿
对应提示差什么包安装即可.出现Cannot Get / ，hexo server 也启动成功，很可能是没有执行npm install

##### 3 hexo 指令简介

	$hexo -h #最有用 没有之一
	$hexo s #本地预览 默认端口4000  端口被占用可选择关闭端口或者选择其他端口 hexo s -p 6666 
	如果出现样式不对 没有加载可以尝试强制刷新或者重启服务
	$hexo s --drafts #将草稿加入本地预览	  
	$hexo g #根据source生成public 在github上的代码就是public中的文件
	$hexo d #部署 部署是根据项目配置文件中的
	$hexo clean #删除生成的public文件
	$hexo new title #在_post下新建文件，不需要加后缀
	$hexo new _draft title #在_drafts下新建草稿
	$hexo publish title #将draft发布
	
	deploy: #通常采用ssh-key模式 不需要再次输入账户密码
		type: git
		repo: git@github.com:yourgithubname/yourprojectname.git #可以多个
		branch: master#your branch
		#message: "update"

#### Next
***

##### 1 \_config.yml
> 两个配置文件不要混淆了，一个是项目跟目录下的\_config.yml 一个是主题下的\_config.yml。项目根目录下得配置文件预览下，通常跟用户的个性设定有关，主题的跟主题参数设置相关，具体可以详细了解下.

##### 2 languages  
> 保存语言key-value值，选择自己需要，其他的可删掉.

##### 3 layout  
> 生成html的模板，这个文件下的东西可以好好了解下，里面主题的头部，脚本，侧边栏，菜单的设定和修改都可以在这里找到。需要自定义主题，可以在预览下找到对应的class name 全局搜索即可，作者采用的命名很规范和释义。我的主题删减了很多部分就是先删除这里的html代码生成的部分. 

##### 4 source  
> 这里面存放的是css部分的东西，修改样式通常在这里修改即可  



主题个性化配置讲的不详细，大家可以到[官方的说明文档](http://theme-next.iissnan.com)查看.

***

##### more:
  
> [我的主题](http://www.nuxseme.com)------[源码](https://github.com/nuxseme/nuxseme.github.com.origin)  
[HEXO+Github,搭建属于自己的博客](http://www.jianshu.com/p/465830080ea9)  
[Hexo 靜態博客使用指南](http://www.jianshu.com/p/73779eacb494)  






	

	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	


