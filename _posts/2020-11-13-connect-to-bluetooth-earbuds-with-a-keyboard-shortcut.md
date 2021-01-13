---
layout: post
title: "Connect to Bluetooth earbuds with a keyboard shortcut"
description: ""
category: Technology
tags: [Bluetooth, earbuds, keyboard, shortcut]
---

For many reasons, I need to connect my earbuds to different computers from time to time. On Mac I
can use Alfred +
[alfred+bluetooth-workflow](https://github.com/tilmanginzel/alfred-bluetooth-workflow), so
convenient! To get similar experience on Linux, I did the following.

First, install `bluez` if not already: `sudo apt install bluez`.

Then find the earbuds address: `bluetooth scan on` or just do it once using `gnome-control-center`.

Create a script with content as the following. This tries to connect to the earbuds for at most
5 times, each with 2 seconds timeout.


```bash
#!/usr/bin/env bash

connect () { /usr/bin/bluetoothctl --timeout 2 connect <address> | grep "Failed to connect"; }

END=5
x=$END
while [ $x -gt 0 ]; do
  connect
  ret=$?
  if [ $ret -ne 0 ]; then
    break
  fi
  x=$(($x-1))
done
```

Put this script somewhere under `$PATH` and create a keyboard shortcut in gnome-control-center. Then
good to go.

It is also possible to use Albert terminal plugin, ~but that opens a new terminal, which is a bit
annoying.~ Updated: holding `Alt` gives more options including running in the background.

