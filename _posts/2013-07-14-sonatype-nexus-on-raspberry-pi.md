---
layout: post
title: "Sonatype Nexus on Raspberry Pi"
description: "Sonatype Nexus on Raspberry Pi"
categories: [Technology, Raspberry Pi, Java]
tags: [Sonatype, Nexus, Raspberry Pi, Java, maven]
---


Ever since phasing out of my super old x86 laptop, and replacing it with a super cheap
raspberry pi, I have been losing my [Sonatype Nexus](http://www.sonatype.org/nexus/).
The reason for needing such a tool is so obvious that I won't say anything about it here.
If you really don't know what it is (of course you do otherwise you wouldn't have come to
this page), please google by yourself.

Now the problem is how to run it on my Raspberry Pi. Initially I thought it would be quite
trivial as it's just a Java application anyway, but I was so wrong. The whole problem was
caused by something called [Java Service Wrapper](http://wrapper.tanukisoftware.com/doc/english/download.jsp).
I don't really want to know what the hell it is because people in Java world are always
reinventing wheels again and again.

The problem was that the jsw shipped with Nexus does not have an arm version. So to solve the
problem I googled a while and ended up with this
[article](http://ti57.blogspot.se/2013/05/apache-archiva-on-raspberry-pi.html). This is a
fantastic article that I need to say big "thank you" to the author.

So now comes the detailed solution.

Note that following steps are all executed on Raspberry Pi.

0. Download [Oracle JDK8](https://jdk8.java.net/download.html) Linux ARM version with HardFP support.
   I assume that you know how slow it is when running OpenJDK on Raspberry Pi, and Oracle JDK7
   does not even have a HardFP version. I'm running XBian which is HardFP, by the way.
   Set your `JAVA_HOME` and `PATH` properly.
   
1. Download jsw version 3.2.3
   [here](http://wrapper.tanukisoftware.com/download/3.2.3/wrapper_3.2.3_src.tar.gz?mode=html).
   I tried to use the latest version as it already has an arm version pre-built but I failed with
   a core dump related with queue something, and then I quit.
   
2. Modify build.xml by commenting out `javah` section because this will lead to a
   [bug](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=4824827).
   
3. Copy `src/c/Makefile-linux-x86-32` to `src/c/Makefile-linux-arm-32`, and then
   add `-lm` to `wrapper` compiling command.
   
4. Shoot `build32.sh` and wait. This will take sometime.

5. Download standard Nexus [here](http://www.sonatype.org/downloads/nexus-latest-bundle.tar.gz),
   and uncompress it to your home directory. Don't try to run it as a root user (ask Sonatype because
   it says so...).
   
6. Modify `conf/nexus.properties` if you want to change default port and work directory. I changed
   work directory to somewhere I mounted via samba to my NAS because there would be no point to store all
   those jar files directly on the Pi unless you have a really big SD card.
   
7. cd into `bin/jsw`, copy `linux-x86-32` to `linux-armv6l-32`, and copy `wrapper_3.2.3_src/bin/wrapper`
   to `linux-arm6l-32` replacing the existing one; cd into `lib` and copy `wrapper_3.2.3_src/lib/libwrapper.so`
   as `libwrapper-linux-arm-32.so`.
   
8. Finally start Nexus by executing `bin/nexus start`. Please watch the log until it has been started
   completely. This would take a very very very long time, so be patient. Don't worry then because after
   being started the performance is quite acceptable.

If everything is correct, you would be able to enjoy your Sonatype Nexus on Raspberry Pi. And by the way,
default administrator is `admin/admin123`.
