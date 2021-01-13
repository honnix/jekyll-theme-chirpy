---
layout: post
title: "Give me back Super+Q"
description: ""
category: "Technology"
tags: [Linux, Gnome, shortcut, keyboard]
---

OK, this has been driving me nuts. I'm so used to `Super+Q` to start Alfred on Mac, and now Albert
on Linux. I set it in Albert settings, but after a while it gets overridden by something and the
shortcut triggers showing Gnome shell dock. This is just ridiculous!

It turns out that an extension, installed by default, named `Dash to Dock`, set `Super+Q` as hotkey
to trigger showing the dock, and that is not configuration in the extension itself, neither in
`Settings`. Good job people!

The fix is pretty simple:

```shell
gsettings set org.gnome.shell.extensions.dash-to-dock hot-keys false
```

Since when Linux applications started to make decision for users, and leave not much spaces to
customize???
