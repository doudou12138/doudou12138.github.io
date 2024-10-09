---
title: BlockingQueue
category: JUC
---
- 什么是阻塞队列?
阻塞队列是JUC中一部分,是一种线程安全的队列.对于take操作,如果此时队列中没有元素,当前线程会阻塞等待,在队列中插入元素后会被唤醒;对于put操作,如果此时队列中元素已满,当前线程会阻塞等待,在队列中取出元素后会被唤醒
阻塞队列有很多实现类: ArrayBlockingQueue, LinkedBlockingQueue,PriorityBlockingQueue

- ArrayBlockingQueue的原理
1. queue基于数组实现
2. 会有一个锁和两个Condition

当put元素时,会加锁,从而保证队列元素的线程安全,同时如果此时元素已满,则会在notFull的Condition上await等待唤醒

- put代码:
```
public void put(E e) throws InterruptedException {
    // 确保传入的元素不为null
    checkNotNull(e);
    final ReentrantLock lock = this.lock;

    // 请求锁，如果线程被中断则抛出异常
    lock.lockInterruptibly();
    try {
        // 循环检查队列是否已满，如果满了则在notFull条件上等待
        while (count == items.length) {
            notFull.await();
        }
        // 队列未满，将元素加入队列
        enqueue(e);
    } finally {
        // 在try块后释放锁，确保锁最终被释放
        lock.unlock();
    }
}
```
