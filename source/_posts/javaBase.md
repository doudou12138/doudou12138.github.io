---
title: java基础
category: java基础
---
- 为什么重写equals()要重写hashCode()
可以借助HashMap定位的逻辑来看,先根据HashCode锁定桶,再在桶中遍历使用equals对比  
如果不重写hashCode,应该判定为相等的实例hashCode却不同,则可能定位到不同的桶,即是HashMap中已经有了,因为在另一个桶中都没有找到,可能导致认为没有这个key,多次放入
Set中也是,equals()方法和hashCode()方法是组合使用的

- 为什么String要设计为不可变的
1. 线程安全
2. 加快速率(创建对象时放心缓存hashcode)
3. 安全性
4. 便于实现字符串池

- Object类中的方法
1. protected Object clone()
2. boolean equals(Object obj)
3. protected void finalize()
4. Class<?> getClass()
5. int hashCode()
6. void notify()唤醒等待该对象的某个线程
7. void notifyAll()唤醒等待该对象的所有线程
8. String toString()
9. void wait()
10. void wait(long timeout)
11. void wait(long timeout, int nanos)

- 为什么推荐使用notifyAll()而不是notify()
首先,notify()会随机唤醒一个休眠线程,而notifyAll()会唤醒所有休眠线程  
如果使用notify()唤醒的线程并不是我们预期的,可能造成安全问题,见如下示例:  
> 一个方法在3\*n+1时+1,否则wait(),一个方法在3\*n+2时+1,否则wait(),一个方法在3\*n时+1,否则wait().上述方法在+1后都会调用notify()方法.三个线程分别执行上述方法.
当数值为3时,线程1,2wait(),然后调用notify()方法,如果唤醒的是线程2则又会进入休眠,此时线程均进入休眠.  

如果所有线程的等待条件都是一样的,则可以考虑使用notify()方法.总之,notifyAll()虽消耗更多资源但安全性更高

- 深拷贝,浅拷贝,引用拷贝
        1. 深拷贝: 开辟一块新的内存空间,将基础属性拷贝进来,对于引用对象属性,将分别开辟内存空间进行深拷贝,然后将新的内存空间的地址赋给新的引用
        2. 浅拷贝: 依旧开辟一块新的内存空间,将基础属性拷贝进来,对于引用对象属性,将直接使用原对象的地址
        3. 引用拷贝: 不会开辟新的内存空间,将直接将之前的的内存地址赋值给新引用

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

- HashMap为什么线程不安全
在java1.7中是因为可能出现环形链表,然后调用get操作就可能出现死循环
在java1.8中可能会出现数据覆盖.当一个位置为空时,如果同时多个put操作命中该位置,则可能下一个put操作会把上一个put操作数据覆盖

- ArrayList的扩容机制
ArrayList底层是用数组实现的,有变量size,capacity,当期望容量大于capacity时,比较1.5*capacity和期望容量,如果期望容量小,则扩容1.5倍.如果期望容量大,则扩容至期望容量(初始化时,第一次添加元素时capacity为10)  
> 可以调用ensureCapacity(int capacity)方法手动扩容
ArrayList没有缩容,但是会把不用的设为null

- HashSet和HashMap的区别
1. HashSet是对Set接口的实现,存储的是一个元素;而HashMap是对Map接口的实现,存储的是key-value键值对
2. HashSet求hashcode使用的是元素,而HashMap使用键值对中的key求hashcode
3. HashSet底层是依靠HashMap实现的,其将元素存在HashMap的key中,value为一个静态的Object()对象

- HashTable和ConcurrentHashMap的区别
1. HashTable锁全表
2. ConcurrentHashMap锁每一个桶头

- 线程安全的集合
HashTable, ConcurrentHashMap, Vector, Stack, CopyOnWriteArrayList, CopyOnWriteArraySet
ConcurrentSkipList, ConcurrentSkipSet, ConcurrentLinkedQueue

- 为什么不推荐使用Stack
Stack是线程安全的集合,很多时候不需要线程安全, 会有额外的资源开销.推荐使用Deque实现栈,具体的实现类有ArrayDeque

- CopyOnWriteArrayList
写加锁,读不加锁. 当写的时候新开一个数组然后将数据写入,完成后将数组的引用指向新数组
数组有volatile修饰,当数组修改后,其他线程也可以立即感知

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

- 什么时候从无锁升级到偏向锁
无锁状态对对象的操作就是CAS操作,如果CAS操作失败则升级为偏向锁

- 什么时候从偏向升级为轻量级锁
发现是偏向锁后会先对比对象对应的monitor的owner和当前线程,如果不同则CAS操作修改,操作失败则升级为轻量级锁

- 什么时候从轻量级锁升级为重量级锁
轻量级锁会自旋,当自旋次数超过一定的次数或者一个线程持有锁,一个在自旋,第三个线程来了则会升级

- 为什么要有偏向锁
在同步块只有一个线程运行时,减小获取释放锁的消耗.偏向锁会CAS markword中的ThreadId.  

- 为什么要有轻量级锁
当多个线程交替的访问共享资源时,减小获取释放锁的消耗.

- 怎么加的轻量级锁? 
在线程的栈中生成一个Lock Record, 并存储一个mark word的拷贝(官方称为Replaced Mark Word),将对象的Markword更新为指向Lock Record.并将Lock Record中的owner设为Markword.

- 轻量级锁为什么比偏向锁效率高
偏向锁只需要CAS一次,而轻量级锁需要CAS多次

- synchronized和ReentrantLock的区别
1. synchronized是关键字,ReentrantLock是类
2. synchronized不需要显式加锁,ReentrantLock需要显式加锁和释放锁
3. RetrantLock还可以实现公平锁,synchronized是非公平锁
4. 实现原理: synchronized是基于jvm实现的,重量级锁是通过Monitor实现的,偏向锁轻量级锁则是通过对象头部实现的. ReentrantLock是通过AQS实现的.

- 如何让threadLocal中的值在父子线程之间传递
使用InheritableThreadLocal(InheritableThreadLocal是ThreadLocal的子类)
底层是在建子线程时浅拷贝了一份线程变量

- StringBuilder和StringBuffer的区别
String,StringBuilder,StringBuffer都实现了CharSequence接口.  
StringBuilder,StringBuffer都是用来构造字符串的类,其中:
        - StringBuilder是线程不安全的,但是性能更高
        - StringBuffer是线程安全的,但是性能更低  

- 如何保证三个线程同时开始执行:
使用CountDownLatch,要想让多个线程同时开始,则让这几个线程对同一个countDownLatch.await()
最后调用countDownLatch.countdown()

- 如何保证三个线程依次执行:
多种手段: 使用CountDownLatch,使用一个volatile变量判断哪个线程执行

- 如何保证三个线程交替执行:
使用Semaphore
~~~
public class Solution{
        Semaphore s1 = new Semaphore(1);
        Semaphore s2 = new Semaphore(0);
        Semaphore s3 = new Semaphore(0);
        public void printAlternative(){
                Thread t1 = new Thread(()->{
                        while(true){
                                try{
                                        s1.acquire();
                                        System.out.println("s");
                                        s2.release();
                                }catch(Exception e){

                                }
                        }
                });

                Thread t2 = new Thread(()->{
                        while(true){
                                try{   
                                        s2.acquire();
                                        System.out.println("o");
                                        s3.release();
                                }catch(Exception e){

                                }
                        }
                });

                Thread t3 = new Thread(()->{
                        while(true){
                                try{
                                        s3.acquire();
                                        System.out.println("p");
                                        s1.acquire();
                                }catch(Exception e){

                                }
                        }
                });

                
        }
}
~~~

- volatile的作用和工作原理
作用:
1. 保证变量在线程间的可见性.即线程A对volatile变量的修改可以马上被其他线程B读取到
2. 防止指令重排序
工作原理:
> 每个线程都不直接与主内存交互,而是和线程的工作内存交互,所以修改不会马上被其他线程读到
1. 对volatile变量的修改,需要强制去刷新主内存,并且将其他线程工作内存中该变量值置失效.
> 这里使用了MESI协议;M:modified 已修改, E:excusive(独占的) S:shared(共享的) I:invalid(失效的)
利用了状态机,在事件的触发下综合数据的会带来状态机状态的轮转和相关的操作.
2. 内存屏障

- 什么是JIT
Java In Time Compiler:即时编译.
java程序会先编译成字节码,然后执行时会通过Interceptor逐行解释,灵活性,实时性较高,但是性能较低.
JIT会把字节码编译,然后再执行,编译完了直接执行效率较高,但是编译也要时间,编译后的字节码还要占用内存
所以对于java程序其热点代码(常用方法,循环)会触发JIT;而对于非热点则解释运行.
