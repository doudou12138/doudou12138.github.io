---
title: 网络分层结构及常见协议
category: 网络
---
## OSI七层结构
![分层结构]()

## TCP/IP四层协议
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/30/1739d1d409e3174b~tplv-t2oaga2asx-zoom-in-crop-mark:1512:0:0:0.awebp)

- ping属于哪一层
ping属于TCP/IP协议的一部分,用于检查网络连通性.使用ICMP协议,属于网络层
> ICMP协议(Internet Control Message Protocal)属于网络层协议

- 传输层的作用:
网络层提供了主机之间的逻辑通道,而传输层提供了不同的进程之间的逻辑通道.

        - 复用和分用:
                - 不同的应用进程的信息通过一个网络层来传输
                - 一个网络层传输的消息发送到多个应用进程中
        - 弥补网络层的不足:
                - 稳定传输,正确传输
                - 拥塞控制,流量控制

- 传输层常见协议:
TCP,UDP

- 有了IP为什么还要MAC
首先MAC地址的出现早于IP地址,在网络上机器较少时,每台机器直接可以根据MAC地址寻址到目标机器.随着机器数量增加,引入了IP地址,
网络可以通过IP地址的分段机制推断出到目标机器的一条路由.
IP用于寻址,路由.MAC地址会在请求到达时做校验,MAC地址是否一致
