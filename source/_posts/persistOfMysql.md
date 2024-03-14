---
title: MySQL持久化
category: mysql
---
- MySql是如何实现持久化的?
先说结论.通过日志系统实现的. InnoDB引擎的redo log和所有引擎都有的bin log.
1. 以InnoDB引擎来说:  
当我们写数据时,首先数据会被写到InnoDB Buffer pool(内存中的缓存池),然后同步地去写redo log,只有在redo log写成功后才能提交事务.(**WAL: write ahead log**)    
然后从redo log到磁盘上的数据的同步,由Mysql中的其他线程来完成.     
> redo log也是在磁盘中,所以redo log写成功后,即使Mysql宕机,没有存入数据,也能通过redo log去同步上  

而在主从复制的架构中,Mysql不仅仅需要维护redo log,还要维护bin log,这时候我们就需要**二次提交机制**:
在上面我们是写redo log后就可以提交事务,但是现在: 
将InnoDB buffer pool中数据修改后,  
先**prapare redo log**,即写redo log并且相关记录打上prepare标签,  
然后写**bin log**
最后将redo log中相关记录打上**commint**状态标签,提交事务  

- 为什么要二次提交?
保证redo log和bin log的一致性,二次提交如果bin log写失败,则事务也无法提交. 
如果bin log写成功,redo log commit失败,mysql重启时会对每个prepare状态的记录进行校验,以binlog中是否写入来决定是提交事务还是回滚事务

- bin log和redo log的不同
1. 提供者: redo log是InnoDB存储引擎提供的, 而bin log是Mysql本身提供的,适用于各存储引擎,也就是说对各存储引擎的修改都会记到bin log
2. 内容格式不同: redo log中记录的是物理格式日志,即对磁盘中每个数据页的修改; bin log中记录的是逻辑格式日志,即记录sql语句
3. 写入时机不同: binlog是在事务完成时一次性写入, redo log是**事务边进行边写**,所以可能多个事务的redo log混在一起
4. redo log是循环写,文件大小固定; bin log是追加写,写完一个文件再写下一个文件


