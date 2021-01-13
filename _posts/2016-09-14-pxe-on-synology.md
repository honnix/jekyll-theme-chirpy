---
layout: post
title: "PXE on Synology"
description: "How to set up PXE on Synology NAS working together with pfSense"
categories: [Technology, Linux, Docker]
tags: [Docker, Synology, PXE, pfSense]
---


As my home network grows more and more complex, I need to find something to make it
even more complex. Yes, why not setting up a PXE server? I actually have and may
have more Linux boxes to install.

While there are many articles talking about setting up PXE on Synolgoy NAS, none of
them can fit my requirement, that is using the most complicated way.

Well I'm lying. I actually want the most simple and flexible approach, which is using
[Cobbler](http://cobbler.github.io).

The Synolgoy NAS I have runs on x86, which means it has the capability to run Docker
properly, and the good news is there are a few existing Cobbler Docker images on
[Docker hub](https://hub.docker.com). But unfortunatley none of them is properly made.

Then this [article](http://container-solutions.com/cobbler-in-a-docker-container/)
is quite helpful except that it doesn't work, at least not very much. The problem is
tftp server simply cannot work without specifying `--net host` when creating the container.

Without `--net host`, Docker will use `iptables` and `docker-proxy` to do NAT to
containers, and the way tftp server works is similar to the active mode ftp server, which
means it initiates connection from an ephemeral port toward the client and of course this
won't work under NAT.

OK. I start from that article to make my own customization, so here it is: 
[docker-cobbler](https://github.com/honnix/docker-cobbler).

The `Dockerfile` is pretty straight forward, installing `cobbler`, `cobbler-web`,
`pykickstart` (for `cobbler validateks`), exposing related port. Since systemd refuses to
work on my NAS (lower version of Docker, maybe?), I need to start `httpd` and `cobbler`
manually.

So, now the problem is how about tftp server. Fortunately I can run it on my NAS directly
by following the
[official document](https://www.synology.com/en-us/knowledgebase/DSM/tutorial/General/How_to_implement_PXE_with_Synology_NAS).

I create a shared folder as `TFTP root folder`, but it's not necessary and it's up to you.
But that folder should be mapped to a cobbler container volume in order for cobbler
to access, for example `-v /volume1/tftpboot:/var/lib/tftpboot`.

`./build.sh` will create the image, and `./start.sh` will start a container using the image.

But before that, let's talk about networking. Did I mention I have a pfSense firewall at home?

![](/assets/img//2016-09-14-pxe-on-synology/home_network.png "Home Network")

pfSense manages DHCP for my home network, so first all I need to configure `next server` and
`Default BIOS file name`. `next server` points to my NAS where tftp is running.

![](/assets/img//2016-09-14-pxe-on-synology/network_booting.png "Network Booting"){:width="400px"}

I assign a virtual IP address to pfSense and port forward that to my NAS, so that I can use
that IP address to access exported ports from the container.

![](/assets/img//2016-09-14-pxe-on-synology/virtual_ip.png "Virtual IP"){:width="400px"}

![](/assets/img//2016-09-14-pxe-on-synology/port_forward.png "Port forward"){:width="600px"}

Note that there is a "black hole" that doesn't exist in my home network to handle all the
other ports, otherwise accessing the virutal IP will bring you to pfSense. You can of course
create firewall rules to further block everything else. As a side note, pfSense does NAT first
and then firewall. In the above screenshots, `192.168.17.4` leads us to cobbler.

Since I don't use cobbler to manage anything, in `settings` file I disable everything.
It is very important to set `server: 192.168.17.4` because this is how cobbler can be accessed
by client, and this IP is used to generate files under `pxelinux.cfg`. Super super important!

That is pretty much everything, hopefully.
