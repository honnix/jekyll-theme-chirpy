---
layout: post
title: "Cobbler in VirtualBox"
description: ""
categories: [technology]
tags: [Cobbler, VirtualBox, PXE]
---

[Cobbler](http://www.cobblerd.org) can help you with all those complicated things for
network installation, i.e. PXE, DHCP, etc.

Here is a brief instruction for trying it out in VirtualBox with Ubuntu as guest OS.

### To set up the machine running Cobbler:

* Create a virtual machine with two NICs, one of which must be `Internal Network` (keeping default
  name), and the IP address could be `192.168.1.2`; the other one can be either `NAT` or `Bridge`.
* Installation under Ubuntu is fairly simple as always by following
  [this article](https://help.ubuntu.com/community/Cobbler/Installation).
* If you want Cobbler to handle everything, modify `/etc/cobbler/settings` by changing
  `manage_dhcp` to `1`, and both `server` and `next_server` to `192.168.1.2`.
* `apt-get install isc-dhcp-server debmirror`
* `cobbler sync` or even `service cobbler restart`.

### To set up a bare metal machine:

* Create a virtual machine with at least one NICs, one of which must be `Internal Network` (keeping
  default name); Settings->System->Boot Order, enable `Network` and put it as the first.
* Boot the machine and you should be able to see PXE menu.
* Then follow standard Cobber document [here](http://www.cobblerd.org/manuals/2.4.0/) to play with it.

### References

* [https://help.ubuntu.com/community/Cobbler/](https://help.ubuntu.com/community/Cobbler/)
* [http://www.webscalability.com/blog/2013/03/testing-out-cobbler-with-virtuabox/](http://www.webscalability.com/blog/2013/03/testing-out-cobbler-with-virtuabox/)
