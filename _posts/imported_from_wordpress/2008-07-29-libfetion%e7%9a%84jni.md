---
title: libfetion的JNI
author: honnix
layout: post
permalink: /archives/178
categories:
  - Java
  - Linux
  - Technology
tags:
  - JNI
  - libfetion
---
这个东西没有想像的好弄，尤其是callback函数的处理，昨天搞到半夜才弄明白。

libfetion里面的callback函数不是只调用一次，而且每次有消息过来就会调一把，这样对于JNI的处理就比较麻烦了。因为是callback，所以callback对象和参数都必须加到Global Reference里去，但是什么时候delete掉呢？之前老是把虚拟机跑死，原因就是一旦callback一次后，我就把这些东西delete掉，以至于libfetion再callback的时候就core了。

弄明白这个原因之后，就有了一个简单的解决办法。每次为某个libfetion的函数注册一个callback对象的时候，把它和参数都存到一个数组中，这个对象和参数一直存在，直到再次为这个函数注册callback对象，或者是JNI_OnUnload的时候把它们清除掉。但是这样也有一个问题，这些注册的对象除非是再次为这个函数重新注册，否则几乎没有可能被从Global Reference里移除掉，所以完全不能依靠finalize了，不过这个也没什么，本来就不该依赖这个东西。