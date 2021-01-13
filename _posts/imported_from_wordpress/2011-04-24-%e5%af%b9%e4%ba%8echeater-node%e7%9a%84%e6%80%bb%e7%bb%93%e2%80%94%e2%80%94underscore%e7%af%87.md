---
title: 对于cheater-node的总结——underscore篇
author: honnix
layout: post
permalink: /archives/726
categories:
  - javascript
  - nodejs
  - Technology
  - Uncategorized
tags:
  - Javascript
  - nodejs
  - underscore
---
javascript确确实实是一个functional的语言，但问题是它本身并没有提供什么方便使用的库。不过还好我们有社区，有无数英雄们默默无闻地做贡献（眼泪花花儿的）。

介绍一下<a title="underscore" href="http://documentcloud.github.com/underscore/" target="_blank">underscore</a>。点过去看看就知道了，亲切得很。

安装很简单，直接npm就行了。不知道npm是什么的，或者google，或者参考[这里][1]。

虽然那里有无数的例子，但这里还是给一个吧：

<div class="wp_codebox">
  <table>
    <tr id="p72646">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
</pre>
      </td>
      
      <td class="code" id="p726code46">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #003366; font-weight: bold;">var</span> loginInfo <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span> <span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
    <span style="color: #003366; font-weight: bold;">var</span> info <span style="color: #339933;">=</span> <span style="color: #009900;">&#91;</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'tz_offset'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'480'</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'username'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'xxx'</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'password'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'yyy'</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'realm'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'Zoning'</span><span style="color: #009900;">&#93;</span>
    <span style="color: #009900;">&#93;</span><span style="color: #339933;">;</span>
    <span style="color: #003366; font-weight: bold;">var</span> _ <span style="color: #339933;">=</span> require<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'underscore'</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #000066; font-weight: bold;">return</span> _<span style="color: #009900;">&#40;</span>info<span style="color: #009900;">&#41;</span>.<span style="color: #660066;">map</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">function</span> <span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">item</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #000066; font-weight: bold;">return</span> <span style="color: #000066; font-weight: bold;">item</span><span style="color: #009900;">&#91;</span><span style="color: #CC0000;"></span><span style="color: #009900;">&#93;</span> <span style="color: #339933;">+</span> <span style="color: #3366CC;">'='</span> <span style="color: #339933;">+</span> <span style="color: #000066; font-weight: bold;">item</span><span style="color: #009900;">&#91;</span><span style="color: #CC0000;">1</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">join</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'&amp;'</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

“\_(info)”返回一个对象，包含一个名叫\_wrapped的property。

<pre>&gt; var _ = require('underscore');
&gt; var w = _([1, 2, 3]);
&gt; w
{ _wrapped: [ 1, 2, 3 ] }</pre>

有了这个东西之后一切都变得美好了，跟所有functional的用法一样，点啊点得就干了很多事儿。不过需要注意的是_(info).map()之后返回的东西就是一个数组了。嗯，不太好……

这个问题人家早就考虑过了：

<div class="wp_codebox">
  <table>
    <tr id="p72647">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
</pre>
      </td>
      
      <td class="code" id="p726code47">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #003366; font-weight: bold;">var</span> loginInfo <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span> <span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
    <span style="color: #003366; font-weight: bold;">var</span> info <span style="color: #339933;">=</span> <span style="color: #009900;">&#91;</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'tz_offset'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'480'</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'username'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'xxx'</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'password'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'yyy'</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #009900;">&#91;</span><span style="color: #3366CC;">'realm'</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'Zoning'</span><span style="color: #009900;">&#93;</span>
    <span style="color: #009900;">&#93;</span><span style="color: #339933;">;</span>
    <span style="color: #003366; font-weight: bold;">var</span> _ <span style="color: #339933;">=</span> require<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'underscore'</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #000066; font-weight: bold;">return</span> _<span style="color: #009900;">&#40;</span>info<span style="color: #009900;">&#41;</span>.<span style="color: #660066;">chain</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">map</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">function</span> <span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">item</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #000066; font-weight: bold;">return</span> <span style="color: #000066; font-weight: bold;">item</span><span style="color: #009900;">&#91;</span><span style="color: #CC0000;"></span><span style="color: #009900;">&#93;</span> <span style="color: #339933;">+</span> <span style="color: #3366CC;">'='</span> <span style="color: #339933;">+</span> <span style="color: #000066; font-weight: bold;">item</span><span style="color: #009900;">&#91;</span><span style="color: #CC0000;">1</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">join</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'&amp;'</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">value</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

先chain一下，玩儿完之后再value一把。这回爽了吧？

顺便说一下，functional这么好的东西为什么会有人跳出来反对呢？

再多废一句话啊。如果你在node的交互式终端里面使用“\_”，你会发现诡异的问题，原因是这个终端默认使用“\_”表示上一个命令的返回值。

 [1]: http://honnix.com/blog/archives/711 "npm安装东西时候的困惑"