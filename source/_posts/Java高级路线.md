---
title: Java高级路线
date: 2018-12-13 09:50:40
tags: Java
---
### 一、java多线程并发编程
#### 1.1 java基础
##### 1.1.1 java程序运行堆栈分析
##### 1.1.2 线程状态
##### 1.1.3 线程中止
##### 1.1.4 内存屏障和CPU缓存
##### 1.1.5 线程通信
##### 1.1.6 线程封闭之ThreadLocal
##### 1.1.7 线程池应用及实现原理剖析
#### 1.2 线程安全之可见性问题
##### 1.2.1 使用volatile解决可见性问题及阻止指令重排序
##### 1.2.2 线程安全之共享资源/不可变性/宽态条件/临界区
##### 1.2.3 线程安全之原子操作
##### 1.2.4 Atomic相关类和CAS机制
##### 1.2.5 Java锁相关术语及同步关键字synchronized详解
##### 1.2.6 Lock接口和ReentrantLock/ReadWriteLock
#### 1.3 JUC并发编程包详解
##### 1.3.1 AQS抽象队列同步器详解
##### 1.3.2 FutureTask源码剖析
##### 1.3.3 并发容器类ConcurrentHashMap/ConcurrentSkipListMap
##### 1.3.4 并发容器类ConcurrentSkipListSet/ConcurrentLinkedQueue/CopyOnWriteArrayList/LinkedBlockingQueue
##### 1.3.5 Fock/Join框架
##### 1.3.6 工具类扩展-信号量和栅栏和倒计数器