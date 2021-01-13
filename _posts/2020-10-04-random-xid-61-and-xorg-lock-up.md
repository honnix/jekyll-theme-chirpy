---
layout: post
title: "Random Xid 61 and Xorg lock up"
description: ""
category: "Technology"
tags: [Linux, Nvidia, Xorg, segfault, Chrome]
---

Well I stole the title from this looog
[thread](https://forums.developer.nvidia.com/t/random-xid-61-and-xorg-lock-up/79731/1). The gist is,
sometimes Xorg gets completely frozen up due to unknown bug in Nvidia Linux driver and the only way
to fix is a reboot.

For me, every time this happened I was browsing some web page using Chrome, so very naturally I got
to blame Chrome, which led me to this Stack Exchange
[question](https://unix.stackexchange.com/questions/562458/segfault-in-google-chrome-is-it-nvidia-card-related-how-do-i-find-out).
But after looking into the details, I believe I am experiencing the same issue reported in Nvidia
forum.

I tried to reproduce it as folk
[saying](https://forums.developer.nvidia.com/t/random-xid-61-and-xorg-lock-up/79731/205) setting
both lower and higher clock to 300, but I couldn't do it within a reasonable amount of time and
load.

This seems to be a complicated issue that could be due to a combination of Nvidia card, driver (I
have `xserver-xorg-video-nvidia-450 450.66-0ubuntu0.20.04.1` at the time of writing) and AMD
chipset. The workaround described in the thread is to set a relatively high lower boundary of the
clock.

I created a systemd service:

```shell
$ cat /etc/systemd/system/set-nvidia-clock.service
[Unit]
Description=Set Nvidia clock

[Service]
Type=oneshot
ExecStart=/home/honnix/bin/set-nvidia-clock

[Install]
WantedBy=multi-user.target
```

With the content:

```shell
$ nvidia-smi -pm 1
$ nvidia-smi -lgc 1000,1725 # I have GeForce GTX 1650 super
```

The drawback is very clear, that this consumes more energy. But still better than having to reboot
my computer and losing stuff. I will be monitoring that thread and hopefully it will be fixed.

