---
title: mysql优化总结
categories:
  - null
date:
---


### mysql
>减少全表扫描
减少输出
减少临时表大小(在mysql引擎处理过程中会产生临时表，临时表大于设定的值或者大于内存时会写入磁盘中，产生大量的IO)
减少mysql函数使用
查询where时尽量带上索引
建立联合索引
sql条件绑定参数，减少解析
精简查询语句，设定查询缓存
优化表结构

### 缓存
>建立缓存层

### 业务
>变通业务需求
改变逻辑实现

<!--more-->
[方法TIPS]

>查看索引使用情况
        如果索引正在工作， Handler_read_key 的值将很高，这个值代表了一个行被索引值读的次数，很低的值表明增加索引得到的性能改善不高，因为索引并不经常使 用。
Handler_read_rnd_next 的值高则意味着查询运行低效，并且应该建立索引补救。这个值的含义是在数据文件中读下一行的请求数。如果你正进行大量的表扫描，
该值较高。通常说明表索引不正确或写入的查询没有利用索引。
       语法：
        mysql> show status like 'Handler_read%';
通过以上步骤查询到效率低的 SQL 后，我们可以通过 explain 或者 desc 获取MySQL 如何执行 SELECT 语句的信息，包括 select 语句执行过程表如何连接和连接 的次序

[sql语句]

>in 和 not in 要慎用，否则会导致全表扫描，
如：
select id from t where num in(1,2,3)
对于连续的数值，能用 between 就不要用 in 了：
Select id from t where num between 1 and 3

like 模糊查询
>有的时候会需要进行一些模糊查询比如
select*from contact where username like ‘%yue%’	 
关键词%yue%，由于yue前面用到了“%”，因此该查询必然走全表扫描，除非必要，否则不要在关键词前加%
改为
select * from contact where username like 'yue%'
若要提高效率，可以考虑全文检索。
而select id from t where name like 'abc%' 才用到索引

应尽量避免在 where 子句中使用!=或<>操作符

> 否则将引擎放弃使用索引而进行全表扫描。
MySQL只有对以下操作符才使用索引：<，<=，=，>，>=，BETWEEN，IN，以及某些时候的LIKE。 
可以在LIKE操作中使用索引的情形是指另一个操作数不是以通配符（%或者_）开头的情形。例如:
    SELECT id FROM  t WHERE col LIKE 'Mich%'; #  这个查询将使用索引，
    SELECT id FROM  t WHERE col  LIKE '%ike';   #这个查询不会使用索引。

应尽量避免在 where 子句中使用 or 来连接条件

>否则将导致引擎放弃使用索引而进行全表扫描，如：
select id from t where num=10 or num=20
可以 使用UNION合并查询： select id from t where num=10 union all select id from t where num=20
在某些情况下，or条件可以避免全表扫描的。
1 .where 语句里面如果带有or条件, myisam表能用到索引， innodb不行。
2 .必须所有的or条件都必须是独立索引
mysql or条件可以使用索引而避免全表

很多时候用 exists 代替 in 是一个好的选择：

>select num from a where num in(select num from b)
用下面的语句替换：
select num from a where exists(select 1 from b where num=a.num)
最好不要使用"\*"返回所有： select * from t ，
用具体的字段列表代替"\*"，不要返回用不到的任何字段。


### COUNT优化：
count(*) 优于count(1)和count(primary_key)
>很多人为了统计记录条数，就使用 count(1) 和 count(primary_key) 而不是 count(\*) ，他们认为这样性能更好，其实这是一个误区。对于有些场景，这样做可能性能会更差，应为数据库对 count(\*) 计数操作做了一些特别的优化。

count(column) 和 count(*) 是不一样的
>这个误区甚至在很多的资深工程师或者是 DBA 中都普遍存在，很多人都会认为这是理所当然的。实际上，count(column) 和 count(\*) 是一个完全不一样的操作，所代表的意义也完全不一样。
　　count(column) 是表示结果集中有多少个column字段不为空的记录
　　count(*) 是表示整个结果集有多少条记录

innodb引擎在统计方面和myisam是不同的，Myisam内置了一个计数器，

>Count(\*)在没有查询条件的情况下使用 select count(*) from table 的时候，Myisam直接可以从计数器中取出数据。而innodb必须全表扫描一次方能得到总的数量
但是当有查询条件的时候，两者的查询效率一致。

主键索引count(*)的时候之所以慢

InnoDB引擎:

>数据文件和索引文件存储在一个文件中，主键索引默认直接指向数据存储位置。
二级索引存储指定字段的索引，实际的指向位置是主键索引。当我们通过二级索引统计数据的时候，无需扫描数据文件；而通过主键索引统计数据时，由于主键索引与数据文件存放在一起，所以每次都会扫描数据文件，所以主键索引统计没有二级索引效率高。
由于主键索引直接指向实际数据，所以当我们通过主键id查询数据时要比通过二级索引查询数据要快。

MyAsm引擎

>该引擎把每个表都分为几部分存储，比如用户表，包含user.frm，user.MYD和user.MYI。
User.frm负责存储表结构
User.MYD负责存储实际的数据记录，所有的用户记录都存储在这个文件中
User.MYI负责存储用户表的所有索引，这里也包括主键索引。


InnoDB 和 myisam 的主要区别

>InnoDB 能达到行锁级别  myisam 达到表锁级别 所以Innodb在高并发处理能力比myisam优秀
InnoDB 支持事务处理
InnoDB 在处理频繁的更新插入操作能力方面比 myisam 优秀

InnoDB 的count(*) 优化

>不要用count(*) 做精确统计  全表扫描
读取INFORMATION_SCHEMA tables的rows作非精确的统计
建立二级索引 secondary index

