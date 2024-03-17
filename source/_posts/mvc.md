---
title: spring MVC
category: spring
---

- Filter和Interceptor的区别
1. 使用范围不同: Filter是基于Servlet的,使用需要依赖Tomcat等容器.Interceptor是Spring提供的
2. 实现方式不同: Filter是通过回调函数实现的,Interceptor是基于AOP实现的
3. 触发时机不同: Filter是在请求进入Servlet容器前处理的,Interceptor是请求进入Servlet容器后处理的
4. 初始化时机不同: Filter随着Servlet容器的初始化而初始化,Interceptor随着spring启动而初始化
