---
layout: post
title: "Get Chrome issue fixed"
description: ""
category: Technology
tags: [Linux, Chrome, Suspend]
---

As said [here]({% link _posts/2020-09-25-all-the-problems-of-linux-desktop.md %}#chrome), I had to
restart Chrome every time the computer wakes up from sleep and that had been very annoying.

Luckily I found a workaround that is to set `--use-gl=desktop` as a Chrome startup option.

These are the steps:

* Remove favorite from Gnome Shell dock
* Open `/usr/share/applications/google-chrome.desktop`
* Search for `Exec`
* Append `--use-gl=desktop`
* Save and quit
* Start Chrome
* Then added it back to Gnome Shell dock
