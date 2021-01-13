---
title: Java里面的Reference
author: honnix
layout: post
permalink: /archives/55
categories:
  - Java
  - Technology
tags:
  - GC
  - Java
  - Reference
---
之前写过一篇关于Java Reference的文章，只放了一个链接；这个星期要开Java的Workshop，所以仔细研究了一把。

<!--more-->

首先是对几个Reachable作定义（其实是从Java Doc抄来的，翻得我实在不爽，直接看Java Doc吧） ：

1.  Strongly Reachable──一个对象是strongly reachable的，当且仅当它能够被某个线程reach到而不需要通过任何的Reference Object
2.  Weakly Reachable──一个对象是weakly reachable的，当且仅当它不是strongly reachable的，而且仅通过Soft Reference就可以被查找到
3.  Phantom Reachable──一个对象是phantom reachable的，当且仅当它既不是strongly reachable的，也不是weakyly reachable的，它已经被finalized了，而且有一些Phantom Reference指向它

<u>Phantom Reference</u>：

*   如果在ReferenceQueue里面读到了一个Phantom Reference对象，说明该reference的referent已经被finalized了，如果它是finalizable的（这样的话需要两次GC才能在ReferenceQueue里面读到），否则一但referent成为phantom reachable的时候reference就被放入ReferenceQueue中。
*   实验表明，如果referent是finalizable的话，要经过两次GC之后才能在ReferenQueue里面读到Phantom Reference，第一次GC时，referent被finalized，但Phantom Reference不会被放到队列中。
*   如果new一个Phantom Reference的时候不传给它一个ReferenceQueue，那这个reference几乎没有任何用处，因为PhantomReference.get()永远返回null。
*   需要注意的是，GC不会调用PhantomReference.clear()，如果不对它显示地进行clear，那么它所引用到的对象就一直以finalized过的状态待在堆里而不被真正的回收，除非所有指向它的Phantom Reference都被clear了，或者它本身变为unreachable了。<font color="#ff0000">这一点和Weak Reference以及Soft Reference都不一样。</font>
*   Phantom Reference一般用来处理一些cleanup的事情：监视某个对象，如果它被finalized之后，释放资源。例如：一个大的图片对象被finalized之后，那么用来读取和处理它的其它一些资源就可以被释放掉了。

<u>Weak Reference</u>：

*   如果在ReferenceQueue里面读到了一个Weak Reference对象，并不能说明该reference的referent已经被finalized了
*   所有对象的finalization都由一个FinalizeQueue来处理，这个queue本身就是一个线程；当GC发现一个对象是Weakly Reachable的时候，就会把它的reference放到ReferenceQueue中，而把这个对象放到FinalizeQueue中；这两个动作的先后顺序我还不是很清楚，但似乎不是特别重要，这里只要知道由这么两件事情应该就足够了
*   GC会主动调用WeakReference.clear()，<font color="#ff0000">这一点和Soft Reference相同。<font color="#000000">这样，即使没有ReferenceQueue我们也可以知道一个对象什么时候被GC发现成为weakly reachable，只要WeakReference.get() == null就可以了。</font></font>
*   <font color="#ff0000"><font color="#000000">Weak Reference一般用来实现一些容器，我们不需要对容器的引用显示地清理（container[i] = null）以防止内存泄露。比较有名就是JDK中的WeakHashMap，它的key是Weak Reference，当key成为weakly reachable的时候，key&value在就会被清除出去。这样一来，我们可以用map保存对象引用，以及它的一些附加信息，当这样对象不再是strongly reachable的时候，我们不必手工清理map，一切都交给GC去做了。</font></font>

<u>Soft Reference</u>：

*   Soft Reference和Weak Reference非常类似，只有一点最大的区别：当一个对象变成Soft Reachable状态的时候，它不一定被GC回收（或者说有很大可能不会被回收），具体行为由虚拟机实现决定，唯一能保证的是，在虚拟机抛出OutOfMemory异常之前，这个对象肯定已经被回收了。
*   Soft Reference的这个特定使得它非常适合做cache，用Soft Reference引用一个大对象，这个对象在某些时候是strongly reachable的，而有些时候又不需要，那么由虚拟机来确定什么时候回收它吧，如果它被回收，那么SoftReference.get()出来的东西就是null，这时候只要再重新实例化一个对象就可以了。