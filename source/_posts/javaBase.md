---
title: java基础
category: java基础
---
- 为什么重写equals()要重写hashCode()
可以借助HashMap定位的逻辑来看,先根据HashCode锁定桶,再在桶中遍历使用equals对比  
如果不重写hashCode,应该判定为相等的实例hashCode却不同,则可能定位到不同的桶,即是HashMap中已经有了,因为在另一个桶中都没有找到,可能导致认为没有这个key,多次放入
Set中也是,equals()方法和hashCode()方法是组合使用的
