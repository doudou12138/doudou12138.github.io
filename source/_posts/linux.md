---
title: linux
category: os
---
- linux如何查看内存
1. 使用命令free -m  (查看的是物理内存)
2. 查看/proc目录下的**meminfo**,其中记录了内存信息

- linux如何查看cpu核数  
在/proc目录下有文件**cpuinfo**,其中记录了cpu的信息  

- 如何查看进程  
1. ps命令
2. 在linux的/proc目录下,有一堆以数字命名的文件,它们是**进程文件夹**,可通过查看进程文件夹的信息来查看.进程文件夹里有个status文件
