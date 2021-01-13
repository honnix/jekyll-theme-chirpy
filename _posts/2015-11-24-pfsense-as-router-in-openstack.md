---
layout: post
title: "pfSense as a proper router in OpenStack"
description: ""
categories: [Technology, Software, Cloud, Network]
tags: [pfSense, OpenStack, Router]
---


Well, this is a little bit cheating because I started with an already made pfSense image by my colleague.
Nevertheless, it's still a long story.

### Fix Disk Error

When making the image, UUID wasn't used in fstab, so when booting the instance it will complain about disk
not found. This is easy to solve. Type `?` and it will show you all available devices. Then type in whatever is
corrrect and in my case is `ufs:vtbd0s1a`. After successfully booted, modify `/etc/fstab` accordingly for both
`/` and `swap`.

### Enable the GUI

Next step is to temporarily assign a floating IP address to pfSense and disable firewall by `pfctl -d` from `Shell`,
then you will be able to access the GUI, but that's not end of the story because you will probably see this error:

    An HTTP_REFERER was detected other than what is defined in System -> Advanced (https://213.159.184.84/).
    You can disable this check if needed in System -> Advanced -> Admin.

After a bit of googling, here is the [solution](https://forum.pfsense.org/index.php?topic=56956.0), in general
you need to add two lines to `/conf/config.xml`:

```
<webgui>
  <nodnsrebindcheck/>
  <nohttpreferercheck/>
</webgui>
```

After that, delete `/tmp/config.cache` and restart `webConfigurator`. Try again and voila, you are in!

### Continue to Tailor pfSense

Setting up WAN, LAN, VPN, etc. All the usual suspects. In my case I have WAN and two LANs named LAN and OPT1.
After everything has been set up, you probably want to change `/conf/config.xml` back and restart pfSense.
Note that almost for every change you made, you probably have to re-execute `pfctl -d` to disable firewall
because pfSense is indeed very secure.

If you find yourself want to access GUI via WAN again, redo the modification and try `enableallowallwan`
from `pfSense Developer Shell`, and execute `pfctl -d` from `Shell`. But at this point you should really
have disabled WAN access because it's not secure. To be honest I'm still not 100% sure how it works, so random
clicks (meaning trying the above steps combinatorially) may do the job.

### Let's start hacking

My requirement is to use pfSense to route packets between two subnets and between subnets and another network
because I need full firewall control. That means I'm not going to use neutron router as gateway.

```

+----+            +----+               +----+       +----+
|    |            |    |               |    |       |    |
|    |            |    |               |    |       |    |
|    |            |    |               |    |       |    |
|    |            |    |               |    |       |    |
|    |            |    |               |    |       |    |
| E  |            | I  |               |    |       |    |
| X  |            | N  |               |    |       |    |
| T  |            | T  |  +---------+  | L  |       | O  |
| E  |            | E  |  |         +--+ A  |       | P  |
| R  |            | R  +--+ pfSense |  | N  |       | T  |
| N  |            | N  |  |         +--+----+-------+ 1  |
| A  |            | A  |  +---------+  |    |       |    |
| L  |            | L  |               |    |       |    |
|    | +--------+ |    |               |    |       |    |
| N  | |        | | N  |               |    |       |    |
| W  +-+ Router +-+ W  |               |    |       |    |
|    | |        | |    |               |    |       |    |
|    | +--------+ |    |               |    |       |    |
|    |            |    |               |    |       |    |
|    |            |    |               |    |       |    |
+----+            +----+               +----+       +----+
               10.0.0.0/24       192.168.101.0/24  192.168.102.0/24

```

So in my case pfSense will be the gateway for LAN and OPT1. To enable this, modify both subnets in OpenStack
by disabling gateway and put static host routes for example `0.0.0.0/0,192.168.101.2` where `192.168.101.2`
is the LAN IP address of pfSense. This will force any virtual machines attached to LAN to use pfSense as default
gateway. Note that you cannot simply put `192.168.101.2` as the gateway IP because this IP is from allocation
pool and neutron doesn't allow that.

Another important thing here is both LAN and OPT1 should be set using static IP address instead
of DHCP, because otherwise they will also pick up the host routes and a loop will be created that no virtual
machine can access external network (somehow external IP address will be treated as a local one and ARP is fired
to get MAC address which is obviously wrong!).

Next is to enable routing between two subnets using pfSense. This requires a little bit hack according
to [this](https://ask.openstack.org/en/question/26980/problem-using-pfsense-vm-inside-a-tenant/). Basically
executing the following commands for both ports of pfSense.

```
neutron port-update <LAN_pfsense_uuid> --allowed-address-pairs
type=dict list=true mac_address=<MAC_LAN_pfsense>,ip_address=0.0.0.0/0

neutron port-update <OPT1_pfsense_uuid> --allowed-address-pairs
type=dict list=true mac_address=<MAC_OPT1_pfsense>,ip_address=0.0.0.0/0
```

So far so good, but when booting up a virtual machine, you will have trouble retrieving metadata from neutron.
Neutron starts a Python process for each router serving metadata for virtual machines attached to it.

We will need to create two routers and one of them attached to LAN and other to OPT1. We don't want them to really
route anything so we will not attach any of them to external network in this case.

To elaborate more of this:

Link-local IP address `169.254.169.254` is used by cloud-init to retrieve metadata. To understand how it works
you can check it [here](https://ask.openstack.org/en/question/66516/instance-can-not-access-openstack-metadata-service/).
Basically a prerouting entry is defined under the router's namespace and that will direct the request towards
`169.254.169.254` to the Python process which is listening on some port.

Then we have to go back to subnet configuration to add more host routes, for example `169.254.0.0/16,192.168.101.1` where
`192.168.101.1` is the IP address of the router.

Now at this point, everything should be working and it's time to add fine grained firewall rules and that part I will not
cover.

### To Summarize

Neutron/OpenStack is not supposed to be used like what's described here so the whole thing is quite a hack:

* First of all we need to have a proper pfSense image for OpenStack;
* then fix disk error followed by some "standard" hacks to bootstrap pfSense;
* after that enable routing through pfSense by disabling gateway and using customized host routes
* finally fix metadata service by creating dangling router and adding additionl customized host routes to
  route link-local request to neutron router
