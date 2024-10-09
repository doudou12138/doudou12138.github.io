---
title: java中的阻塞和唤醒
category: JUC
---
- 对比Object.wait()和Condition.await()
1. Object.wait()基于对象的隐式锁,需要拿到对象的隐式锁后才能调用wait方法;Condition.await()基于ReentrantLock,只有拿到该锁后才能调用
2. Condition更加灵活,可以对于一个ReentrantLock创建多个Condition实例
3. 调用方法不同Object.wait()对应唤醒方法Object.notify(),Object.notifyAll();Condition.await()对应唤醒方法Condition.signal(),Condition.signalAll()

- 在调用wait(),await()阻塞后,都会释放获取的锁.唤醒后要再获取锁
