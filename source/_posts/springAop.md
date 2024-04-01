---
title: spring事务支持
category: spring
---
- 什么时候@transactional()注解会失效?
1. 注释的方法非public
2. 注释的方法被本类中方法调用(因为是基于aop实现的,本类的方法调用不会打到代理对象)
3. 手动try catch处理了异常
4. 数据库引擎不支持事务
5. 错误的异常类型参数填写
