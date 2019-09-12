---
title: bitbucket安装
date: 2019-09-12 01:19:23
categories: git
---

### docker-compose.yml
	
	version: '3'
	services:
	  bitbucket:
	    image: atlassian/bitbucket-server
	    volumes:
	      - ./data:/var/atlassian/application-data/bitbucket:rw
	    ports:
	      - 7990:7990
	      - 7999:7999
	    container_name: bitbucket
	   
	
	mikdir data
	docker-compose  up -d
	Creating network "bitbucket_default" with the default driver
	Creating bitbucket ... done

### setting up


1. 打开web ip:7990  进行初始配置  
  ![start](/img/bitbucket-start.png)  


2. 选择初始语言 后续再安装中文包   
![select language](/img/bitbucket-select-language.png)


3. 注册bitbucket 账号生成证书  
	![generate license](/img/bitbucket-generate-license.png)
4. 证书选项  
   ![install license](/img/bitbucket-install-license.png)
5. 安装证书  
	![setting license](/img/bitbucket-setting-license.png)
6. 管理员设置  
	![admin setting](/img/bitbucket-admin-setting.png)
7. 登陆	  
	![login](/img/bitbucket-login.png)

8. 下载中文语言包  
	地址：https://packages.atlassian.com/maven/com/atlassian/translations/bitbucket-language-pack-zh_CN/  
	![down chinese language pack](/img/bitbucket-down-chinese-language-pack.png)

9. 上传并安装语言包  
	![upload language pack](/img/bitbucket-upload-language-pack.png)
10. 语言设置成功  
	![chinese](/img/bitbucket-chinese.png)

### 注意事项

1. 测试版本选择了内部的数据库，如果要用于生产环境，最好外接数据库
2. docker 挂载的data 需要备份容灾


