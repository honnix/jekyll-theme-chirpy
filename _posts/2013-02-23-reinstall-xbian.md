---
layout: post
title: "Reinstall XBian"
description: ""
categories: [Technology]
tags: [Raspberry Pi, XBian, Upgrade, Reinstall]
---


看到XBian的alpha5出来了，手贱去升级，结果搞出了不少麻烦。

先看[这里](https://github.com/xbianonpi/xbian/issues/294)吧。主要是因为libc的升级导致的，再加上alpha5改了不少东西，结果就是被卡在了一个不能升级也不能降级的状态，最终没办法只好重装了。还好之前把一对配置文件放到了github上，省了再折腾的时间。

不过这也好，终于下决心换了个class10的sd卡。SanDisk的Extreme Pro (SDSDXPA-008G-X46)，8G。看看这里sd的[兼容性列表](http://elinux.org/RPi_SD_cards)，还是很有帮助的。
