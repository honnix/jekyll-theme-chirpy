---
layout: post
title: "Fix Messages Login Failure"
description: ""
categories: [Technology]
tags: [Mac, Message, Facetime, Login, Failure, Serial Number]
---

After upgraded to Mavericks, my `Message` and `Facetime` stopped working completely, and I couldn't even login.
After googling for a while, I realized it was the problem of my serial number! Check [here](http://rogersm.net/icloud-problems-mountain-lion-serial-number).

This article is pretty good but the software it provides did not work for my MBA'11. I got
an error something like `loadefidriver image start failed ...`. Then I kept on searching until
I hit a file named as `blank board serializer 3t106 063-7137.zip`. Sorry I don't think it is
legal to download or share, but I really don't have time to go to Apple Store. So please don't
ask for it.

This file has size of around 21.6MB, and it contains two versions: 3T106 and 063-7137-A.
The later one worked for me. But I had to convert the .dmg file into read-only first,
otherwise I couldn't restore it to my USB disk. I got an error like `invalid argument`.
To convert it, load it to `Disk Utility`, right click and choose convert. Then follow the guide
you may find anywhere to restore it to a USB disk or CD, then boot and burn serial number.

Make sure you are burning the right serial number because this is write-once!
