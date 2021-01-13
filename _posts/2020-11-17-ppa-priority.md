---
layout: post
title: "PPA priority"
description: ""
category: Technology
tags: [Linux, PPA, APT, priority]
---

So I discovered [this awesome theme](https://github.com/pop-os/gtk-theme). Installation was quite
smooth and it looked gorgeous.

But after `sudo apt update`, I found hundreds of packages to be upgraded. Weird. It turned out that
the PPA contains not only the theme but also other packages for
[Pop!_OS](https://pop.system76.com/). To prevent installing/upgrading other packages accidentally, I
gave the PPA a lower priority:

```
sudo tee <<EOF /etc/apt/preferences.d/system76-pop-pin >/dev/null
Package:  *
Pin: release o=LP-PPA-system76-pop
Pin-Priority: 400

Package:  pop-theme
Pin: release o=LP-PPA-system76-pop
Pin-Priority: 600

Package:  pop-gnome-shell-theme
Pin: release o=LP-PPA-system76-pop
Pin-Priority: 600

Package:  pop-gtk-theme
Pin: release o=LP-PPA-system76-pop
Pin-Priority: 600

Package:  pop-icon-theme
Pin: release o=LP-PPA-system76-pop
Pin-Priority: 600

Package:  pop-sound-theme
Pin: release o=LP-PPA-system76-pop
Pin-Priority: 600
EOF
```
