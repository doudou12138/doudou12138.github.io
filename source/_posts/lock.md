---
title: 锁介绍
category: 锁
---
- 如何选择自旋锁,非自旋锁
自旋锁在占锁失败后会重试,非自旋锁在占锁失败后不会.  
所以自旋锁会暂时不切换线程,而是本线程继续占用cpu资源,而非自旋锁会休眠当前线程,然后锁释放后重新尝试占有锁并恢复现场
综上,如果锁占有的时间比较短,或者说自旋能较快成功(不会占用过多的cpu资源)则采用自旋锁,减少cpu切换及恢复现场的开销;如果不能(可能会自旋较久才能成功)则采用非自选锁(减少无意义的cpu资源消耗)  

- 