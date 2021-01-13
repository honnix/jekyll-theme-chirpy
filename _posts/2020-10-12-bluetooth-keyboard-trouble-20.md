---
layout: post
title: "Bluetooth keyboard trouble 2.0"
description: ""
category: Technology
tags: [Linux, Gnome, Bluetooth, Keyboard]
---

Man, this is disgusting!

After upgrade to Ubuntu 20.10, the Bluetooth keyboard issue I [mentioned before]({% link
_posts/2020-09-25-all-the-problems-of-linux-desktop.md %}#bluetooth) seems to be fixed, at least it
has appeared to be better. However another thing is broken!

After waking up, it seems my Bluetooth
keyboard gets reset to the initial state disregarding all of my personalization, such as repeat rate
and interval, as well as cap lock remapping.

~I have not found any known issue of this~ (Updated:
[this](https://gitlab.gnome.org/GNOME/mutter/-/merge_requests/1553) seems to be the fix) and I could
not find any sensible workaround. I end up with running a script manually every time waking up.

```shell
#!/bin/sh

gsettings set org.gnome.desktop.peripherals.keyboard repeat false
gsettings set org.gnome.desktop.peripherals.keyboard repeat true
gsettings set org.gnome.desktop.input-sources xkb-options "[]"
gsettings set org.gnome.desktop.input-sources xkb-options "['caps:ctrl_modifier']"
gsettings set org.gnome.desktop.peripherals.mouse speed 1.0
```

This works basically by toggling things back and forth. As long as it works.
