---
title: SQL优化
category: MySQL
---

- limit,offset是怎么运行的
以select * from test where val>3 limit 1000,5为例:
(val是非唯一索引)
搜索时会走到val索引上, 遍历相应的1005项并且对于每一项都会去回表,到聚簇索引上获取数据,返回最后的五项
缺点: 
1. 前1000项回表是不必要的,并且获取数据还有1000次随机IO(当然覆盖索引则不会回表,但是我们不应该去限制用户select哪些字段)
2. 前1000项的数据不应该被写到buffer pool中,占据buffer pool的空间

- 怎么优化limit offset
1. 将查询条件落到id上
```
select * from test where id>=
(select id from test where val >3 limit 1000,1)
limit 5
```
没有回表,5次随机IO;这是基于id自增的基础上的(找出第1000个id,以第1000个id为基准比他大的x个)
2. 子查询优化
```
select a* from test a ,(select id form test where val>3 limit 1000,5) b
where a.id=b.id 
```

- PageHelper是什么
PageHelper是一款开源的分页插件,其作用是在sql查询时拦截SQL并添加上相应的count查询,limit条件;由此来得到分页结果.  
使用时,首先添加PageHelper的PageInterceptor的拦截器插件,然后
1. 代码中执行pageHelper.startPage(pageNum,pageSize)方法,(会保存一个本地线程变量pageNum,pageSize下一次查询使用)则该线程下一次查询会自动加上分页逻辑
2. 配置pageInterceptor的的属性supportMethodsArguments为true,则在mapper中传入名为pageNum,pageSize的参数,则会自动进行分页  

- PageHelper的原理
PageInterceptor实现了Mybatis的Interceptor接口,在执行查询时会对SQL进行拦截,如果要分页,会在sqlBuilder后append **limit x / limit x,y**

- PageHelper的坑
如果startPage()后,不进行查询,则下一次可能出现意料外的分页
如果不想分页,但传入了pageNum,pageSize,则会出现意料外的自动分页
大数据量,大偏移量下的低效率问题(因为低层是limit,offset同上)  
