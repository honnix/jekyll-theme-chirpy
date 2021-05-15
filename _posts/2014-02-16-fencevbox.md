---
layout: post
title: "fencevbox"
description: ""
categories: [Technology]
tags: [VirtualBox, Cobbler, Power Management, fencing, fencevbox]
---

As a continuation of previous article introducing Cobbler inside VirtualBox,
this one will talk about [power management](http://www.cobblerd.org/manuals/2.4.0/5/6_-_Power_Management.html).

First of all, as a principle of no reinventing of the wheel,
[here](https://code.google.com/p/fencevbox/) is the fence agent for VirtualBox.
But there are a few bugs which are slightly annoying, so I clone it and make it
on [Github](https://github.com/honnix/fencevbox).

I suppose you already have Perl execution environment, then just follow the steps below:

1. Download either from the original repository or from my cloned one;
2. Copy `fence_vbox` to `/usr/local/sbin`;
3. `cobbler sync` or even `service cobbler restart` and `service apache2 restart`;
4. Create a file named `fence_vbox.template` under `/etc/cobbler/power/` with content:
   ```
   vmname=$power_id
   vboxhost=$power_address
   login=$power_user
   action=$power_mode
   ```
5. In Systems configuration of Cobbler Web UI, fill in following info:
   ![Power Management](/assets/img//2014-02-16-fencybox/power_mgmt.png "Power Management")
   IP address `10.0.2.2` here shows that `NAT` is used and this IP points to your host;
6. As this Perl script uses simple ssh login to interact with VBoxManage, it will prompt for
   password, so you need to generate public key for `root` user inside the virtual machine and
   put it to the user's home of the host machine, which is `$HOME/.ssh/authorized_keys`, and
   make sure to do an ssh login at least once to accept your host machine as a "known host";
7. Then run `cobbler system powerstatus --name=<name of the system>`, you should be able to
   check the status.

BTW, I do think Cobbler does not respect
[FenceAgentAPI](https://fedorahosted.org/cluster/wiki/FenceAgentAPI#agent_ops),
because `powerstatus` checking should result return value as `2` as specified by the API spec,
but Cobbler keeps checking again and again and finally gets timeout, and if I put the return
value as `0`, it is then OK.
