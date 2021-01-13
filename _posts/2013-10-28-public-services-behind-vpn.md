---
layout: post
title: "public services behind vpn"
description: ""
categories: [technology]
tags: [vpn, http, public services, iptables]
---

So the idea is simple: I need to offer public services, e.g. http, ssh, on my Raspberry Pi behind a VPN connection.
But what's the difference w/ and w/o a VPN connection?

The thing is with a VPN connection on my Raspberry Pi, there is no problem receiving packets, but all responding packets
will be sent to the VPN server I'm connect to ,and the server has no idea how to deal with those packets. So the VPN
server behaves like a blackhole eating all those suspicious packets.

The solution is quite straight forward but not trivial. So it's worth recording down.

The original solution comes from [Keven Wang](http://www.keven4ever.com/?p=36). Most steps described by Keven also
apply to my problem, but there are something more.

First, create a new routing table in file `/etc/iproute2/tables` with a new line for example `2 no_vpn`.

Then create a script with following content:

    #!/bin/sh

    ip route add default via 192.168.17.1 dev wlan0 table no_vpn # by default go to my home router to allow NAT there
    ip route add 192.168.17.0/24 dev wlan0 table no_vpn # if it is from my home network, without this local access will not work
    ip rule add from all fwmark 1 table no_vpn # all packets marked as "1" go to no_vpn routing table

    # http, bt, ssh
    iptables -t mangle -A OUTPUT -m multiport -p tcp --sports 80,9091,22 -j MARK --set-mark 1

    # ddns
    iptables -t mangle -A OUTPUT -p tcp -d 202.105.21.204 -j MARK --set-mark 1
    iptables -t nat -A POSTROUTING -p tcp -d 202.105.21.204 -j SNAT --to 192.168.17.9 # if it is the server orginated packet towards my DDNS service provider, after routing, modify source IP address as Raspberry Pi's local IP address otherwise it would be the IP address assigned by VPN connection

Last step, after having connected to VPN server, run this script. Without VPN connection, this script also doesn't hurt.
