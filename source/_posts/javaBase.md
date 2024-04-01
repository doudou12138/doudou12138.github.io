---
title: java基础
category: java基础
---
- 为什么重写equals()要重写hashCode()
可以借助HashMap定位的逻辑来看,先根据HashCode锁定桶,再在桶中遍历使用equals对比  
如果不重写hashCode,应该判定为相等的实例hashCode却不同,则可能定位到不同的桶,即是HashMap中已经有了,因为在另一个桶中都没有找到,可能导致认为没有这个key,多次放入
Set中也是,equals()方法和hashCode()方法是组合使用的

- 讲讲java中的集合
java中的集合主要是两个顶级接口:Collection和Map,Collection接口下又有接口List,Set,Queue  
其中Map侧重于存储键值对,List侧重于存储元素的有序,Set侧重与元素的独一无二性,Queue侧重于排队  
具体实现的话:
List的实现有ArrayList,LinkedList,Vector,Stack(FILO)
Queue的实现有PriorityQueue(优先队列),LinkedList
Map的实现有HashMap,HashTable,LinkedHashMap,TreeMap(有序)
Set的实现有HashSet,LinkedHashSet,TreeSet

- ArrayList和LinkedList的区别
1. ArrayList基于数组实现的,LinkedList是基于(双向)链表实现的
2. ArrayList根据下标搜索快,LinkedList的增加删除操作效率更高
3. LinkedList还实现了Queue的接口,可以用作队列

- HashSet和HashMap的区别
1. HashSet是对Set接口的实现,存储的是一个元素;而HashMap是对Map接口的实现,存储的是key-value键值对
2. HashSet求hashcode使用的是元素,而HashMap使用键值对中的key求hashcode
3. HashSet底层是依靠HashMap实现的,其将元素存在HashMap的key中,value为一个静态的Object()对象

- java创建线程的方法
1. 继承Thread类,并重写run方法,thread.run()执行线程
2. 实现Runnable接口,并重写run方法,然后作为target参数构造thread
```
Thread thread = new Thread(runnable);
```
3. 实现Callable接口,并重写call方法,以callable为参数构造FutureTask,以futureTask为参数构造thread
```
Callable<Integer> callable = new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                System.out.println("calling....");
                return 1;
            }
        };

        FutureTask<Integer> ft = new FutureTask<>(callable);

        new Thread(ft).run();
        System.out.println(ft.get());
```

- synchronized给实例方法加和给静态方法加有什么区别
给实例方法加synchronized本质上是给this加
给静态方法加本质上是给class加

- synchronized底层的实现
重量锁(jdk1.6之前只有重量锁)的实现:  通过Monitor实现的,当线程进入同步块时,会用CAS的方式将Monitor的owner设为自己的id,count+1,如果已经是本线程则recursion+1,当释放同步块时,count-1,recursion-1.recursion=0表示释放锁了
monitor又是通过MonitorEnter和MonitorExit指令实现的  

- jdk1.6为什么要对synchronized做优化
因为这之前synchronized是使用Monitor实现的,Monitor又是使用os Mutex lock实现的,操作系统线程之前切换需要从用户态切换到内核态,性能较低

- 为什么要有偏向锁
在同步块只有一个线程运行时,减小获取释放锁的消耗

- 为什么要有轻量级锁
当多个线程交替的访问共享资源时,减小获取释放锁的消耗

- synchronized和ReentrantLock的区别
1. synchronized是关键字,ReentrantLock是类
2. synchronized不需要显式加锁,ReentrantLock需要显式加锁和释放锁
3. RetrantLock还可以实现公平锁,synchronized是非公平锁
4. 实现原理: synchronized是基于jvm实现的,重量级锁是通过Monitor实现的,偏向锁轻量级锁则是通过对象头部实现的. ReentrantLock是通过AQS实现的.
