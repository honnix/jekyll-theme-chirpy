---
layout: post
title: "Install Windows 10 Pro"
description: ""
category: Technology
tags: [PC, Windows]
---

So, I have a Windows 10 Pro license lying around somewhere for a long time, and now it is finally
more useful than registering a virtual machine.

I thought booting from a USB key should be out of question nowadays, well to some extend it was, but
there were caveats.

### FAT32 vs. NTFS

What the heck! Why is this still a problem, 10 year later the last time I had to deal with it.

Downloading a Windows 10 Pro ISO was lightning fast thanks to morden broadband; copying files into a
USB key was amazingly fast too; however, there was this file (install.wim) bigger than 4GB, and of
course that didn't fit into `FAT32` file system. I used a Mac to create the bootable USB key, so
`NTFS` was not (easily) available.

The solution was pretty simply though. When doing this on Windows, one can follow [this
guide](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/split-a-windows-image--wim--file-to-span-across-multiple-dvds)
from Microsoft. When doing on Mac or Linux, one can use [wimlib](https://wimlib.net/). I did it on a
Mac, so it was something like `wimlib-imagex split
/Volumes/CCCOMA_X64FRE_EN-US_DV9/sources/install.wim /Volumes/WIN10/sources/install.swm 4000`.

### GTP vs. MBR

I created the bootable USB key using `GPT` partition table, because my [hardware]({% link
_posts/2020-09-13-back-to-pc.md %}) is new enough to support `UEFI`. Well it could boot the
computer, and guide me through the installation process. However, after copying all files to disk
and ready to reboot, an error message appeared saying `something something wrong`, which I don't
remember the details. I thought it was an one off problem, so I tried a few more times, but all of
them failed at the same point. I should note that after partitioning, the installation process
showed me a warning that the partitions didn't match some sort of Windows 10 [default partitioning
style](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/configure-uefigpt-based-hard-drive-partitions),
but I double checked a few times, it was exactly like what the doc said.

Just before I almost threw the USB key out of my window, I thought about partition table, so I tried
`MBR` instead of `GPT` when creating the bootable USB key. Guess what, it all worked out, and I
still partitioned my disk using `GPT` partition table.

This is the thing I have not yet figured out why. But, as long as it worked.

No, I have not enabled secure boot.
