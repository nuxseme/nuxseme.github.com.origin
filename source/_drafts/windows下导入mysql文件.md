---
title: window下导入mysql文件
categories:
  - null
date:
---
导入时报错unknow command '\\'

在服务器导入的character可能不一致，修改--default-character-set=utf8

eg:mysql> mysql -h127.0.0.1 -uroot -p --default-character-set=utf8
	导入时文件路径中的斜杠注意修改 d:\\xx\\xxx.sql