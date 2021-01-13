---
layout: post
title: "Remap shortcuts"
description: ""
category: Technology
tags: [Linux, keyboard, shortcut, remap]
---

Jumping back and forth between Mac and Linux is OK most of the time, but one the pain point has been
keyboard shortcut.

This is annoying when using Chrome, and is extremely annoying when copying things from Chrome to
terminal (I configured Alt-C and Alt-V for copy&paste in gnome-terminal) or vice versa.

[xkeysnail](https://github.com/mooz/xkeysnail) is great and can easily remap shortcuts. However it
is a bit cumbersome to use. [kinto](https://github.com/rbreaves/kinto) offers a nice integration by
offering xkeysnail systemd service, a UI to quickly generate a default xkeysnail config file (might
be good enough for some users, but unfortunately not suitable to me), a debugger and a more or less
standardized file structure.

This is what I configured in `$HOME/.config/kinto/kinto.py`:

```python
# -*- coding: utf-8 -*-
# autostart = true

import re
from xkeysnail.transform import *

# Use the following for testing keymaps
# xbindkeys -mk
excluded = [x.casefold() for x in ["emacs", "gnome-terminal"]]
excluded_str = "|".join(str(x) for x in excluded)

define_multipurpose_modmap(
    # {Key.ENTER: [Key.ENTER, Key.RIGHT_CTRL]   # Enter2Cmd
    # {Key.CAPSLOCK: [Key.ESC, Key.RIGHT_CTRL]  # Caps2Esc
    # {Key.LEFT_META: [Key.ESC, Key.RIGHT_CTRL] # Caps2Esc - Chromebook
    {                                         # Placeholder
})

define_keymap(lambda param: not re.compile(excluded_str, re.IGNORECASE).search(param),{
    K("LM-T"): K("LC-T"),
    K("LM-W"): K("LC-W"),
    K("LM-F"): K("LC-F"),
    K("LM-R"): K("LC-R"),
    K("LM-C"): K("LC-C"),
    K("LM-V"): K("LC-V"),
    K("LM-L"): K("LC-L"),
    K("LM-LShift-T"): K("LC-LShift-T"),
})
```
