---
title: 配置Tor使用Bridge
author: honnix
layout: post
permalink: /archives/460
categories:
  - GFW
  - Linux
  - Technology
tags:
  - bridge
  - Tor
---
我比较白痴，首先澄清一个概念，使用Bridge和作为Bridge是两个概念。

*   使用Bridge是指作为client，使用已有的Bridge连接到网络。
*   作为Bridge是指把自己变成一个Bridge server。

貌似不能既使用Bridge又作为Bridge。:(

使用Bridge很简单，发送邮件给bridges@torproject.org，邮件title为get bridges，就可以收到一个回复，大概是这样的：

> [This is an automated message; please do not reply.]
> 
> Here are your bridge relays:
> 
> bridge 85.176.137.125:443  
> bridge 125.121.20.246:9002  
> bridge 79.182.101.170:8080
> 
> Bridge relays (or “bridges” for short) are Tor relays that aren’t listed  
> in the main directory. Since there is no complete public list of them,  
> even if your ISP is filtering connections to all the known Tor relays,  
> they probably won’t be able to block all the bridges.
> 
> To use the above lines, go to Vidalia’s Network settings page, and click  
> “My ISP blocks connections to the Tor network”. Then add each bridge  
> address one at a time.
> 
> Configuring more than one bridge address will make your Tor connection  
> more stable, in case some of the bridges become unreachable.
> 
> Another way to find public bridge addresses is to visit  
> https://bridges.torproject.org/. The answers you get from that page  
> will change every few days, so check back periodically if you need more  
> bridge addresses.

把里面那几行bridge列表加到/etc/tor/torrc文件的最后，再加上一行UseBridges 1。重启tor，如果同时使用privoxy的话把它也重启一下好了。现在的FireFox根本就不需要这个东西了，可以直接使用socket代理。

作为Bridge的话，在/etc/tor/torrc里加上这么几行：

> SocksPort 0  
> ORPort 443  
> BridgeRelay 1  
> Exitpolicy reject \*:\*

自己对照着看看，不要直接加上去。

为什么不作为一个normal relay呢？因为从我们这里出去的话不是在浪费其它人时间么！所以Bridge就行，不要直接出去。