---
layout: post
title: "RapidMiner Server and R extension"
description: ""
categories: [Technology]
tags: [RapidMinder, R, RapidAnalytics]
---


I was very surprised that I couldn't found any article talking about how to make
RapidMinder Server (formerly RapidAnalytics) work with R extension. All those
articles are discussing how to install that with RapindMiner Studio, like
[here](http://exceptionaloutcomes.wordpress.com/2011/05/22/tips-on-installing-rapidminers-r-extension-on-a-windows-machine/), [here](http://activeintelligence.org/blog/archive/install-r-extension-for-rapidminer-on-ubuntu-linux/),
or [here](http://otherside.mimijidi.com/entries/configuring-r-extension-in-rapidminer-under-linux/).

However, the problem was I couldn't figure out where to configure `rapidminer.r.native_lib`.
I tried to give a `-D` jvm option, but it didn't work; and since it is running inside jboss,
all those *rc files would not be read. I even tried to read the source code to understand
what was happening.

Finally I realized that I needed to configure it in the GUI! There is a system settings menu item
where you can add more configurations. So basically you add a new one named `rapidminer.r.native_lib`
with the value which is a path pointing to your jri library, and that's it. Oh, yes, you need to
restart RapidMinder Server. BTW, the configuration is written into database.

Maybe I was too stupid, but anyway I hope Google would index this article very soon.

P.S. [This thread](http://rapid-i.com/rapidforum/index.php?topic=5877.0) actually discussed this question.
