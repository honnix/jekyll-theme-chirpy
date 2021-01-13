---
title: APE解码器MAC在Snow Leopard上的问题
author: honnix
layout: post
permalink: /archives/564
categories:
  - Mac
  - Technology
tags:
  - APE
  - Mac
  - Snow Leopard
---
其实也不是多大的问题，只是方向不对，搞了很久才解决。

移植MAC的这哥们儿提供了一个configure，当然，是根据Leopard生成的，或者更早Tiger，然后对于SL就不工作了。问题在于port里面的yasm不再接受-fno-common这个选项，只能用-fmacho32或者-fmacho64。

我满心欢喜地统统配成64位，结果一跑就core；不过还好开源，直接gdb上，调了半天才发现很多指针直接cast成int，而SL里int仍然是32位的，所以用截了一半儿的地址肯定会core。统统改成32位再来，就好了。

顺便改了一下configure.in，然后重新autoconf一下，貌似这样比较完美。

SL里面看core文件生成在什么地方用这个命令：sysctl kern.corefile；生成core文件么，同样也是：ulimit -c unlimited。