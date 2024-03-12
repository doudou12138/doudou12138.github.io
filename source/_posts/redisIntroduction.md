---
title: redis概述
category: redis
--- 
- redis为什么快
1. redis是基于内存,redis的操作在内存中进行,内存操作效率比磁盘的效率高得多
2. redis是采用单线程模型(事件处理采用单线程),没有多线程之间切换的上下文切换成本
3. redis采用IO多路复用模型,
4. redis的有着丰富的数据结构且设计巧妙,效率高

- redis和memcached的区别
redis和memcached都基于内存,且可以做缓存但有着以下区别:
1. redis支持更丰富的数据类型(string, set, zset, list, hash, geo, bitmap, hyperloglog);而memcached只支持key-value
2. redis有持久化功能(aof,rdb);而memcached没有,服务宕机了数据就丢失了
3. redis原生支持集群
4. redis支持订阅模型,lua脚本,事务等功能,而memcached不支持
