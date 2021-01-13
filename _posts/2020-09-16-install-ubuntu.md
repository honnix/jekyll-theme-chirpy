---
layout: post
title: "Install Ubuntu"
description: ""
category: Technology
tags: [PC, Linux, Ubuntu]
---

OK, now we are talking.

One of the biggest reasons that I wanted to get a PC, was to get better performing emacs because
macOS sucks.

### Why Ubuntu?

This was not an easy choice. Already before configuring the hardware, I had looked into different
distros. Ubuntu is not the most popular distro but there are a few things I consider:

0. I have passed the age when I believed proprietary software was evil, so I won't reject anything
   that can make my PC work better. Ubuntu has very good support of this but Debian doesn't.
0. I seriously considered Gentoo. I used to be a heavy Gentoo user, to the extent that I could spend
   days trimming down my kernel to precisely fit my hardware. However, Gentoo is falling behind a
   lot, so I wasn't so sure how it could support the hardware.
0. There are many Debian/Ubuntu spin-off, but almost all of them are lacking a corporate to back up.
   Canonical has good resource and money to keep their desktop edition moving forward.
0. Back in the day, I used to receive Ubuntu discs and distribute them to my classmates, and I loved
   those days!
0. I used Ubuntu server a lot for my job, so it just feels more natural to use it as desktop OS.

### Get it installed

Again USB key came to rescue. It has become a lot easier when making a bootable USB key from ISO
nowadays, thanks to [ISOLINUX hybrid
mode](https://wiki.syslinux.org/wiki/index.php?title=ISOLINUX#Isohybrid). I basically followed the
official instruction from [Ubuntu
website](https://wiki.syslinux.org/wiki/index.php?title=ISOLINUX#Isohybrid), but skipped the stupid
Etcher thing because it was extremely slow and didn't work. I wonder whether Canonical got paid the
this company.

It didn't long to get a bootable USB key, except that I couldn't get the boot process pass GRUB. I
chose the default to boot and immediately got a complete display mess up, with nothing moving
forward. After a series of retries and Googling, I still made no progress. Accidentally I chose the
second GRUB menu item and then it worked! I guess it was due to my 4K monitor not playing well with
the default graphic driver that the installation process used. The second menu item is something
called safe graphics. There are quite some hits on Google, for example [this
one](https://askubuntu.com/questions/1138137/what-is-safe-graphics-mode).

Although the installer managed to recognize existing Windows installation and its partitions,
according to Internet (TM), it is still safer to partition manually, so I did it and it was smooth.
In fact I didn't encounter any other issue during the installation. _But tons of problems came
after, and I will talk more in the following posts._

### To swap or not to swap?

No I do not and will likely never need hibernation, so I definitely didn't have to create a 32GB
swap partition! But, the question was should I have it at all? Simply put, I read this faq
(partially unfortunately) and decided to give it 4GB. I could have chosen to use swapfile, but I
forgot that option and I didn't read the full faq. Well decision made and it's not a big deal as
disk is cheap.
