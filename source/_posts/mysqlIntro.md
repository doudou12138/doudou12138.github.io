---
title: Mysql
category: 数据库
---
- 一条sql的执行过程
1. 首先执行器根据Mysql的执行计划来查询数据,先会到缓存池中去查询(buffer pool),如果没有则再到数据库中查询, 将查询到的数据放入缓存池(buffer pool)
2. 在数据放入缓存池的同时,将数据写入undo log
3. 更新的动作在buffer pool中完成,同时将更新后的数据写入redo log buffer中
4. 完成后就可以提交事务,提交的同时会发生以下:
        - 将redo log buffer中数据刷入redo log file
        - 将本次操作写入bin log中
        - 将bin log名以及数据在bin log中的位置记录到redo log中,同时在redo log中置commit

- int(10)中10的意思是?
10的意思是显示的长度,不是存放数据的大小限制.
