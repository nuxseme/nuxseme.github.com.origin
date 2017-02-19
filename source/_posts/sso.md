---
title: sso
categories: sso
date: 2017-01-25 11:18:24
---


Jasny SSO 流程解析

	1.client访问a.com,a.com校验client cookie是否含有token
	2.没有token,a.com生成token(建立局部会话)，让client重定向到sso server,url携带token,a.com的站点信息，校验码去建立全局会话
	3.server 校验参数，确认a.com是认证的站点，开启全局会话。开启session,sso_broker_token_sha1=>session_id
	4.client持有a.com cookie.token,持有server cookie.PHPSESSION。client重定向到a.com
	5.a.com获取到token,向server请求用户数据。携带参数sso_broker_token_sha1
	6.server收到请求，根据sso_broker_token在文件系统中获取session_id
	7.server根据session_id重建session,并获取sso_user对应的username
	8.未找到username,重定向到登录页面
	9.a.com 收到token,局部会话已存在。获取username,password请求登录，携带参数sso_broker_token_sha1
	10.server校验username,password。根据sso_broker_token_sha1还原session,设置sso_user=>username。返回用户信息
	11.再次访问a.com，a.com校验token,携带sso_broker_token_sha1获取用户数据，还原session，获取sso_user

	12.client访问b.com,b.com建立局部会话，设置token
	13.client重定向到server,server 校验b.com信息，确认b.com可信
	14.server建立全局会话，sso_broker_token_sha1=>session_id，这个session_id来自浏览器phpsession
	如果出现登录了a.com，关闭浏览器之后在重新登录a.com,a.com处于登录状态，因为a.com会将sso_broker_token_sha1拿到server还原session,
	而这个时候登录b.com，由于浏览器PHPSESSION已经消息，server会对b.com建立新的全局session
	15.client重定向到b.com，b.com校验token,携带sso_broker_token_sha1获取用户数据
	16.server用sso_broker_token_sha1重建session环境，获取sso_user 返回用户数据

	client<->broker : 局部会话
	client<->server: 全局会话

	client 保留局部会话的token 可能保留全局的PHPSESSION
	broker 生成局部会话的token 生成sso_broker_token_sha1 用这个到server换取全局的session_id,获取用户数据
    server 在绑定client访问broker事件，生成新的或者采用PHPSESSION 获得sso_broker_token_sha1=>session_id。登录的时候，写入sso_user=>username
			获取用户信息的时候，根据传入的sso_broker_token_sha1,获取session_id 重建session,获取sso_user
		

	生活实例：
	
	 你 动物园  海洋馆   售票中心

	1 你到了动物园，想进动物园。动物园的管理员发现你没有他给你的动物园的票据，就发你一张动物的门票，叫你到售票处给钱登记
	2 你跑到售票处，售票处的管理员，看你的动物园票据和动物的信息，先确定是本园区的动物园
	3 然后你付完钱，管理员记录下你有权限进入动物园，记录下你的身份证号码，然后让你去动物
	4 你又到了动物园，动物园的管理员看你有他发的动物园票据，然后打电话问询售票处管理你是否给钱，以及你登记的姓名
	5 售票处管理员拿到动物园报的身份证号码，发现你没有录你的个人信息。回复动物园管理员，找不到你的信息
	6 动物园管理员让你提交信息去登陆
	7 售票处管理员验证username password。返回动物园管理员你的信息。
	8 你拿着动物票据现在可以随意进入动物园了，必要的时候动物园管理员会把你的身份证号拿去售票处换取你的个人信息

	9 现在你想去海洋馆，馆长发现你没有海洋馆的票据，先发你一张海洋馆的票据。让你到售票处给钱
	10  你又到了售票处 拿出你的身份证，售票员你看 ，你已经买了通票，记录下你有权限进入海洋馆，让你去海洋馆
	11 你到了海洋馆，馆长发现你有海洋馆的票据，就将你的要去海洋馆这件事去问售票处你是否有权限，售票处返回的个人信息
	12 这个时候你就可以拿着海洋馆的票据随意进入海洋馆了

	

	