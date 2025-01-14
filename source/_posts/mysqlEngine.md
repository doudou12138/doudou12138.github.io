---
title: MySQL存储引擎
category: 数据库
---
- MySQL有哪些存储引擎:  
可以在mysql中使用以下命令查看mysql支持的存储引擎:
   ```
        show engines;
   ```
最常用的存储引擎主要有以下三种:
   - InnoDB
   - MyISAM
   - MEMORY  
其中InnoDB是Mysql默认使用的存储引擎

- InnoDB与MyISAM的对比
1. InnoDB支持事务,MyISAM不支持事务
2. InnoDB支持行级锁,在有写操作的情境下,并发能力更强. MyISAM只支持表级锁
3. InnoDB支持外键, MyISAM不支持外键
4. InnoDB是簇聚索引(文件为.idb),MyISAM是非簇聚索引(文件类型为.MYD和.MYI)

- InnoDB与MyISAM的选择:  
在大多数请求都是读请求时,使用MyISAM,读效率更高,占用空间更小

- 簇聚索引与非簇聚索引的区别  
![](https://ask.qcloudimg.com/http-save/yehe-2823867/2q05hsflfa.jpeg)
簇聚索引索引与数据在一起,数据就存放在索引B+树的叶子结点中.辅助索引的叶子结点带有主键id
非簇聚索引 索引与数据不在一起,索引B+树持有数据地址的指针.  
- 聚簇索引具有唯一性
- 聚簇索引适合在排序场景使用,非聚簇索引不适合
- 聚簇索引适合范围查找,非聚簇索引不适合范围查找
- 聚簇索引维护索引很昂贵,特别是插入数据后导致主键被更新要分页时

- 为什么聚簇索引推荐使用自增ID:
聚簇索引的数据的物理存放数据和索引顺序是一致的,即如果索引是相邻的,那么对应的数据在磁盘中也会是相邻的.  
如果是自增ID的话,插入数据,只需要对磁盘一页一页写就好.而如果是无序的ID,那么插入数据时,可能插到数据之间,导致重新分页等,使效率下降
> 为什么聚簇索引不用UUID?为什么使用UUID写的效率可能还不及MyISAM

- 为什么索引使用B+树而不使用HashMap
1. hash只能匹配相等, 不能实现范围查找
2. 当需要按照索引进行order by时,hash值没办法支持排序
3. 组合索引可以支持部分索引查询,如(a,b,c)只使用ab查询;但是使用hash的话,组合索引就是把abc和并hash,部分的话就走不上索引  
4. 数据大时,hash冲突概率大

