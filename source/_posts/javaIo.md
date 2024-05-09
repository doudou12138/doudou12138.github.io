---
title: java IO
category: java基础
---

- 字节流和字符流
1. 字节流是给计算机看的,二进制;字符流是给人看的,特殊的带编码的二进制流
2. 字节流是InputStream,OutputStream;字符流是Reader,Writer
3. InputStream,OutputStream,Reader, Writer都实现了Closable, OutputStream和Writer实现了flushable, Reader实现了Readerable,Writer实现了Appendable

- linux的IO类型
阻塞式IO(等着输入)
非阻塞式IO(调用IO,没准备好,则退出,待会再试)
多路复用IO(使用一个线程监听多个文件描述符 select poll epoll)\
信号驱动IO(在数据放入内核后,信号通知线程将数据copy到应用空间)
异步IO(调用IO,将数据拷贝到应用空间后再通知线程)

- IO与NIO的差别
1. IO以字节流的形式处理数据,简单且优雅;而NIO以块的形式处理数据,处理速度比流更快
2. IO阻塞的,NIO支持多路复用
