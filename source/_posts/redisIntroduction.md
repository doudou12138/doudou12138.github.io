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
5. memcached的过期策略是惰性删除,而redis同时使用了惰性删除和定期删除

- redis的过期策略
惰性删除 + 定期删除

- redis的淘汰策略
1. Allkeys-random
2. Allkeys-lru
3. 

- redis中过期时间和key在一起吗
不在.  
```
typedef struct redisDb{
    dict *dict;  
    dict *expires;
    ....
}
```
其中dict是保存键值对的,expires是保存key和对应的过期时间long long类型  

- 什么是IO多路复用
当多个客户端与服务端建立连接时,每个连接(socket)都会生成一个fd(文件描述符,毕竟linux中一切皆文件).正常的每个连接都用一个线程来监听,而IO多路复用则是使用一个线程来监听所有的连接对应的fd.  
redis中的多路复用则使用一个线程来监听客户端连接的多个fd,然后将对应的事件交给事件处理器.
他有select, poll, epoll等实现方式

- select, poll, epoll的区别
select,poll,epoll都是IO多路复用的实现方式.其中:
1. select方式线程会轮询所有的fd(所以fd越高,性能越低,线性降低),并检测是否有事件.并且select最多只支持1024个fd
2. poll方式线程也是轮询fd,但是**没有最大文件描述符数量的限制**,因其基于链表存储
3. epoll方式不轮询fd,而是为fd提供一个回调,再准备好后就通过回调将事件放入**就绪队列**,线程只用判断就绪队列是否为空,比轮询fd快得多 O(n)->O(1)

- 为什么lua脚本能保证原子性
redis是单线程模型,并且它会把lua脚本当成一个整体来看待.所以一次只会有一个lua脚本执行,并且lua脚本的执行不会被其他操作中断

