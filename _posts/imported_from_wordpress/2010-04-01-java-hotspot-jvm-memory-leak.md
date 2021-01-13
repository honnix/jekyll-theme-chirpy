---
title: Java HotSpot JVM Memory Leak
author: honnix
layout: post
permalink: /archives/547
categories:
  - Java
  - Technology
tags:
  - AttachCurrentThread
  - DetachCurrentThread
  - HotSpot
  - JVM
  - Memory Leak
  - ParkEvent
---
频繁地调用AttachCurrentThread和DetachCurrentThread，在并发的情况下会有比较严重的Memory Leak，在一个CPU的机器上几率很小，但是多个或者多核的机器上很容易跑出来。

问题出在ParkEvent链表上（其实有问题的还有Park链表），对它的读写并没有全局加锁。具体可以参考源码：thread.cpp。

解决方法也很简单，调用者自己加把锁就行了。