---
title: JVM内存模型
category: JVM/面经
---
- JVM结构主要分为四个部分
1. 类加载器
2. 内存区域
3. 执行引擎
4. 本地库接口

- JVM内存如何划分:
1. 程序计数器(记录下一条指令地址)
2. 虚拟机栈(栈帧为最小单元,内有局部变量表(基本数据类型和复杂类型的引用),操作数栈,返回地址,动态链接)
3. 本地方法栈
4. 堆(对象和方法的存储,字符串常量池(jdk7后))
5. 元空间(jdk8) (运行时常量池,类的描述信息)

其中前三者为线程私有,每个线程都有各自的.  堆与元空间是线程共享的.  

- 堆的结构划分
新生代和老年代(1:2)
新生代又分为Eden区,survive0,survice1(又叫from和to)
> from和to不固定,谁空谁就是to
比例为8:1:1
新生代:老年代,以及新生代内部的内存比例都是可配的

- 垃圾回收机制  
新生代 复制算法  
老年代 标记整理  

- 垃圾回收算法的优缺点
1. 标记清除
    1. 首先通过可达性分析进行标记
    2. 然后遍历进行清除
优点: 简单粗暴
缺点: 遍历两次效率低,容易出现内存碎片
> 产生内存碎片带来的影响:   
当来了一个大对象时,由于全是碎片,导致容纳不下,提前发起gc

2. 标记整理
优点: 不会产生内存碎片,较简单高效,内存使用率高
缺点: 局部变量转移会导致效率变低

3. 复制算法
![](https://s2.51cto.com/oss/202203/21/3413e365954f52e4468691303d6aefce186a7f.png)
缺点: 
   - 内存使用率低, 需要留下一半空间用于垃圾回收,因此正常存储只能使用不到一半的空间.
   - 当存活很多对象时,效率低
优点: 不会产生内存碎片,简单高效

- 如何判断对象是否存活
1. 引用计数法
就是为每个对象添加一个计数器,当该对象被引用的时候,就为计数器加一.当对象的引用失效(出作用域)后,计数器减一. 每当对象计数器减为0时,就回收. 当对象被回收时,其引用的对象的计数器减一  
缺点: 不能解决对象循环引用的场景, 引用计数器加大了开销
优点: 执行简单,效率高,适合实时环境
> java程序没有采用引用技术法
2. 可达性分析
从GC Roots出发搜索,搜索过的路径为引用链.当对象不能通过引用链与GC root连通时,判定为不可达,可以回收
优点: 可以解决对象循环引用的场景
缺点: 相较而言实现复杂

- 哪些可以做GC roots?
1. 虚拟机栈中的引用
2. 方法栈中的引用
3. 元空间中类静态属性的引用
4. 元空间中常量的引用

- 讲一下hotSpot的垃圾收集器
hotSpot中实现了多种垃圾收集器,并且没有哪个垃圾收集器是最好的,也没有哪个垃圾收集器是万能的.常见的有:
   - Serial收集器:
        单线程gc,同时应用程序需要暂停
   - ParNew(Serial的多线程版本)
        多线程gc,同时应用程序需要暂停
   - Parallel Scavenge
        多线程gc,新生代采用标记复制,注重吞吐量(处理用户代码的时间/所有时间)
   - Parallel Old:
        多线程gc,老年代采用标记整理算法
   - CMS:
        目标: 获得最短回收停顿时间,
        采用标记清除算法
        第一款可以让**回收线程与用户线程基本上同时工作**的收集器
        > jdk9中已经被弃用CMS, G1收集器为JDK9后的默认收集器
   - G1: 
        一款面向服务器的,主要针对多处理器,大内存的机器.满足GC时间的同时,还具备高吞吐量的特征


- 默认的垃圾收集器  
jdk8以前:  
新生代:Parallel Scavenge  老年代:Parallel Old  
jdk9~21: 新生代老年代均采用G1