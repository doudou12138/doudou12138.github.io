---
title: sql注入
category: 安全
---
- 什么是sql注入
sql注入是一种很常见的网络攻击方式,其原理是传给服务端的参数是一个非常规的格式,并且这个参数在参与拼接SQL语句时,通过拟合SQL语句的结构,改变了SQL语句的执行方式

举个例子:
select * from user where 'username'='${username}' and `pwd`='${pwd}'

如果前端传过来的pwd为 1' or 1=1
select * from user where 'username'='admin' and `pwd`='1' or 1=1

那么黑客就能登录上该用户账号了

- Mybatis如何防止sql注入
1. 使用#代替$
使用`pwd`=${pwd},那么在拼接SQL语句时,会直接将参数放入SQL语句中
而是用#,那么会用""包裹参数再放入SQL语句中
> 能预防大部分SQL注入
2. Mybatis使用MySQL的预编译
SQL注入生效的时间点主要在SQL的编译期间,MyBatis在SQL执行前,会先把SQL语句发给数据库进行编译;执行时,直接使用编译好的SQL,替换占位符"?"就可以  
```
//预编译后
select * from user where `username`=? and `pwd` = ?
```
也就是说预编译后,SQL语句的执行逻辑就确认好了.非常规的的内容进来也不能改变SQL的执行逻辑