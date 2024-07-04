---
title: 洗牌算法
category: 经典算法
---

- 什么是洗牌算法
是一种将数组元素随机排序的经典算法。

- java支持洗牌算法API
Collections.shuffle(list)

- 如何实现：
Fisher-Yates shuffle算法
```
public void shuffle(int[] nums){
    int len = nums.length;
    Random random = new Random();
    for(int i=len-1;i>=0;--i){
        int x = random.nextInt(i+1);
        int tmp = nums[x];
        nums[x] = nums[i];
        nums[i]=tmp;
    }
}
```
思路： 从数组中随机取一个数字移入新数组。旧数组是nums： index0～i， 新数组是 i～len。