---
title: Mysql中的锁机制
category: mysql
---
- MySQL中有哪些锁
全局锁, 表级锁(表锁,意向锁,元数据锁(DML时),AUTO-INCE锁),行级锁(记录锁,间隙锁,Nect-key Lock)  
乐观锁(Mvcc), 悲观锁  

全局锁主要应用于**全库逻辑备份**时

- 什么是意向锁
意向锁是加在表上的锁,分为共享意向锁和独占意向锁,这个锁不需要使用者显式加,当操作数据库时数据库会自动加锁.  
在对数据加共享锁时,会为其表加上共享意向锁. (ex: 查询某表数据时)  
在对数据加独占锁时,会为其表加上独占共享锁. (ex: 更新某表中数据时)  
如此,**可以快速判断表中是否有共享锁,独占锁**,否则当想要对表加共享锁/独占锁时,就需要遍历记录,看有没有记录被加了行级锁  
> 注意:普通的select是不会加锁的,mvcc机制(乐观锁). 除非是select ... from tablename lock in share mode  