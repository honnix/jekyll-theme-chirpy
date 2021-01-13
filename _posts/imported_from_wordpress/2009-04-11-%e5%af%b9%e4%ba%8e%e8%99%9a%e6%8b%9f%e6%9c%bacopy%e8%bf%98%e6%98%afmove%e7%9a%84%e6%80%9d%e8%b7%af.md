---
title: 对于虚拟机copy还是move的思路
author: honnix
layout: post
permalink: /archives/305
categories:
  - Linux
tags:
  - MAC地址
  - 网卡
  - 虚拟机
---
问题是这样的：

> 创了一个虚拟机，里面跑SuSE Linux，整个虚拟机拿给别人用的时候，会碰到copy还是move这样的疑问，目前看来，最大的区别在于网卡的MAC地址。如果是copy，MAC地址会重新生成，udev也会重新创建一个网络设备名，原来是eth0的话，现在就是eth1，这个还不是最关键的问题，关键在于SuSE Linux的网络配置文件名是和MAC地址绑定的，所以MAC地址变了，这个配置文件名也要相应地改变；如果是move，MAC地址保持不变，一切都没有问题，但是如果同一个虚拟机在一个主机上move两次，嘿嘿，MAC可就要冲突了。

简单来说，有两个解决方案：

1.  每次都选择move，根本就不支持一个虚拟机在一个主机上move两次，也就是说，从我这里release出去的不同版本不能同时启动。
2.  每次都选择copy，提供一个脚本修改网络配置文件名。

对于第二个解决方案，脚本的内容大致是这样：

<div class="wp_codebox">
  <table>
    <tr id="p3051">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
</pre>
      </td>
      
      <td class="code" id="p305code1">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/sh</span>
&nbsp;
<span style="color: #007800;">suffix</span>=<span style="color: #000000; font-weight: bold;">`</span><span style="color: #c20cb9; font-weight: bold;">ifconfig</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">grep</span> <span style="color: #ff0000;">'HWaddr'</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">awk</span> <span style="color: #ff0000;">'{print tolower($5)}'</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">sed</span> <span style="color: #ff0000;">'s/:/\\:/g'</span><span style="color: #000000; font-weight: bold;">`</span>
&nbsp;
<span style="color: #c20cb9; font-weight: bold;">mv</span> <span style="color: #000000; font-weight: bold;">/</span>etc<span style="color: #000000; font-weight: bold;">/</span>sysconfig<span style="color: #000000; font-weight: bold;">/</span>network<span style="color: #000000; font-weight: bold;">/</span>ifcfg-eth-id-<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #000000; font-weight: bold;">/</span>etc<span style="color: #000000; font-weight: bold;">/</span>sysconfig<span style="color: #000000; font-weight: bold;">/</span>network<span style="color: #000000; font-weight: bold;">/</span>ifcfg-eth-id-<span style="color: #007800;">$suffix</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">/</span>etc<span style="color: #000000; font-weight: bold;">/</span>init.d<span style="color: #000000; font-weight: bold;">/</span>network restart</pre>
      </td>
    </tr>
  </table>
</div>