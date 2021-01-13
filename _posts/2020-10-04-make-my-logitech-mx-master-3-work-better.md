---
layout: post
title: "Make my Logitech MX Master 3 work better"
description: ""
category: "Technology"
tags: [Linux, Logitech, Mouse]
---

I love my Logitech MX Master 3, to the extent that I bought two of those, one at home and one at the
office. While it works most of the time, I do have encountered problems related to high resolution
scrolling as described in the previous [post]({% link
_posts/2020-09-25-all-the-problems-of-linux-desktop.md %}#mouse).

Unfortunately, Logitech does not offer software to configure their mice on Linux.
[Solaar](https://github.com/pwr-Solaar/Solaar) is great to get bootstrapped but as far as feature
goes, it misses many. After a bit more search, try and error, I found
[logiops](https://github.com/PixlOne/logiops) that provides a lot more features and customization.

Installation on Ubuntu is fairly straight forward by following the
[instructions](https://github.com/PixlOne/logiops#dependencies), although I was a bit surprised
there is no pre-built deb. My configuration of `/etc/logid.cfg` is as following:

```
devices: (
{
    name: "Wireless Mouse MX Master 3";
    smartshift:
    {
        on: true;
        threshold: 20;
    };
    hiresscroll:
    {
        hires: false;
        invert: false;
        target: false;
    };
    dpi: 1200;

    buttons: (
        {
            cid: 0xc3;
            action =
            {
                type: "Gestures";
                gestures: (
                    {
                        direction: "Up";
                        mode: "OnRelease";
                        action =
                        {
                            type: "Keypress";
                            keys: ["KEY_LEFTMETA", "KEY_D"];
                        };
                    },
                    {
                        direction: "Down";
                        mode: "OnRelease";
                        action =
                        {
                            type: "Keypress";
                            keys: ["KEY_RIGHTALT"];
                        };
                    },
                    {
                        direction: "None"
                        mode: "NoPress"
                    }
                );
            };
        },
        {
            cid: 0xc4;
            action =
            {
                type = "ToggleSmartshift";
            };
        }
    );
}
);
```

The name (`Wireless Mouse MX Master 3`) might be different, and to find what it is, manually run
`sudo logid -v` and check the output.

ArchLinux wiki has a nice [page](https://wiki.archlinux.org/index.php/Logitech_MX_Master) with a lot
more information.
