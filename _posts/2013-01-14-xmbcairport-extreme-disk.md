---
layout: post
title: "Raspberry Pi上XMBC连接Airport Extreme Disk"
description: ""
categories: [Technology, Software]
tags: [Raspberry Pi, XBMC, Airport Extreme, Disk, SMB]
---


网上随便搜搜很多人谈到这个问题，有的说从XBMC里面直接用afp协议连，有人说手工mount，但在我这里都不行，不知道什么原因。

直接从XBMC里面连，说是网络问题，根本不通；用afpfs-ng，mount上的目录权限不对，除了root谁都访问不了。正当抓狂之时，冒着必死的决心试了试SMB，居然可以了。诡异的是Aiport Utility里面根本没有配置SMB的地方。

好吧，可以从Raspberry Pi上面看我硬盘里面存的电影了。
