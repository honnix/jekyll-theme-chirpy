---
layout: post
title: "Boot to Windows"
description: ""
category: Technology
tags: [Linux, Windows, Grub, Blueooth]
---

Of course I default to boot into Linux.

But the problem is, with a Bluetooth keyboard, it is simply not possible to navigate through Grub
menu if I want to boot into Windows.

There is a workaround that requires booting into Linux first! That is good enough for me because I
spend most of the time in Linux and the boot process is lightening fast anyway.

First, open `/etc/default/grub` and add/change `GRUB_DEFAULT=saved`, and then create a script (e.g.
win) with content:

```shell
#!/usr/bin/env bash

set -euo pipefail

sudo grub-reboot 2  # make sure this is the right index of Grub menu entry poiting to Windows
sudo reboot
```

Run `win` to boot into Windows.
