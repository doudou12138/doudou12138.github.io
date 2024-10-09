---
title: Mysql中的日志
category: MySQL
---

- mysql中有哪些日志
主要是三种日志: redolog, undolog, binlog  
其中redolog和undolog都是innoDB引擎具有的,而binlog是数据库Server层面的,即所有存储引擎都具有

- redolog和binlog的区别
前者是innoDB引擎所有的,后者是mysql Server层面的,即所有存储引擎都具有  
redolog存储的是逻辑日志,即xx数据页xx偏移量写入数据xxx, binlog存储的是逻辑日志,即sql
存储空间不同, redo日志组是有限的,需要循环写, binlog写完一个文件再新建一个文件
作用不同,redolog主要是用于掉电恢复,崩溃恢复;binlog主要用于全部备份

redolog也是双存储结构,有独立的Buffer
- redolog buffer什么时候会进行刷盘:
log buffer空间不足(50%)
事务提交时
脏页刷新
定时进程,固定频率刷新
正常关闭服务器

undolog的存储形式
undolog也是逻辑存储,记录的是sql语句.比如执行的sql是一个insert指令,undo log中就会存储对应的delete指令  
undolog的改动也会同步到redolog中
