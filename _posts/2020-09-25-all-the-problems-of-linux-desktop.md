---
layout: post
title: "All the problems of Linux Desktop"
description: ""
category: Technology
tags: [Desktop, Linux, Ubuntu]
---

I can't believe after so many years, Linux desktop still sucks. I have to confess that, this was one
of the biggest reasons I bought a PC and installed Linux, to kill boring stay-at-home time. So, I'm
complaining.

### Bluetooth

In year 2020, I think it is fair for me to have both my keyboard and mouse connected via Bluetooth.
How hard can that be? Hell it was hard.

The first time booting into the OS, there is no problem; however as soon as I put the computer to
sleep (`suspend` as named by Linux) mode, and when it wakes up, it refuses to connect, as if the
kernel module were unloaded.

It turns out that I am not alone, there are many articles, Q&As around this issue, and a bug report
that doesn't seem to be prioritized.

Alright, but I don't want to use a cable. I hate cables. I hate them. Luckily I got inspiration from
[this
thread](https://askubuntu.com/questions/1036195/bluetooth-doesnt-work-after-resuming-from-sleep-ubuntu-18-04-lts).

As always, nothing found on the Internet about how to fix a Linux issue would just work (TM). I had
to make some change to adapt my hardware (?). I have no idea why. Here is my waking up hook script:

```shell
#!/bin/sh

case $1 in
  pre)
    systemctl stop bluetooth
    modprobe -r btusb
    ;;
  post)
    modprobe btusb
    systemctl start bluetooth
    ;;
esac
```

Give it a name, `chmod +x` and put it under `/lib/systemd/system-sleep`. And... most of time it
works, or I just need to sit back and wait for half a minute to let it think. I also find that first
using a software on-screen keyboard to login via GDM could make the connection restored a bit
faster. I have no idea why either, as most things under Linux are magical. Well, as long as it
works.

### Chrome

Again, sleep and wake-up under Linux is a disaster. I think I can understand this. As a server,
Linux is rock solid and a server never needs to sleep. But this is a desktop, being able to sleep or
suspend is pretty reasonable I would say.

After waking up from sleep, Chrome becomes completely unusable. The display is heavily distorted and
gets blacked out.

Guess what, I'm not alone either! And guess what, nothing could fix my issue!! So, I ended up
creating a bookmark (pointing to `chrome://restart`) to start chrome after coming back from sleep.

I tried both stable and beta version, the same result. Nothing works!

### WiFi

I guess that was extremely strange and creepy. After waking up from sleep (??????/??), WiFi stopped
working completely. I tried everything I could think of, driver, firmware, reboot, shutdown, etc. Of
course nothing worked!! At the end I picked the USB key and decided to reinstall the OS. Ironically,
even after booking into installer, there was still no WiFi and the kernel module simply refused to
work. I almost went on wiping out the root partition, but for some reason I thought about giving it
another try. I rebooted the computer, and guess what, it came back. I was speechless. Well, as long
as it works.


### Mouse

Anything could go wrong will go wrong, including mouse of course.

I guess I should blame myself for buying Logitech mouse because Logitech is famous for not doing
great providing Linux driver. Well I guess even if it does GNU world would blame it for not making it
"free".

I have Logitech MX Master 3. It mostly works, but the scrolling is like shxx. It goes way too fast
and completely out of control and doesn't matter via Bluetooth or unifying connector. Luckily I
found [Solaar](https://github.com/pwr-Solaar/Solaar) that works to some extent, at least most of
the time for the scrolling problem. I say most of the time because sometimes when the computer
reboots or wakes up from sleep, the scrolling could go nuts again. The solution is pretty, just run
Solaar with under `sudo`, without taking any other action. Amazing!
