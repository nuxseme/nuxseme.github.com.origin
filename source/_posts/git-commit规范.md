---
title: git-commit规范
categories: git
date: 2018-02-09 16:40:58
---


### message 格式  
	
	<type>(<scope>): <subject>
	// 空一行
	<body>
	// 空一行
	<footer>
	
### type 

* feat：新功能（feature）
* fix：修补bug
* docs：文档（documentation）
* style： 格式（不影响代码运行的变动）
* refactor：重构（即不是新增功能，也不是修改bug的代码变动）
* perf: 优化相关，比如提升性能、体验
* test：增加测试
* chore：构建过程或辅助工具的变动
* revert: 回滚到上一个版本


> 标题行：50个字符以内，描述主要变更内容  
> 主体内容：更详细的说明文本，建议72个字符以内。 需要描述的信息包括:
>> * 为什么这个变更是必须的? 它可能是用来修复一个bug，增加一个feature，提升性能、可靠性、稳定性等等
* 他如何解决这个问题? 具体描述解决问题的步骤
* 是否存在副作用、风险?  
 
>尾部：如果需要的化可以添加一个链接到issue地址或者其它文档，或者关闭某个issue。