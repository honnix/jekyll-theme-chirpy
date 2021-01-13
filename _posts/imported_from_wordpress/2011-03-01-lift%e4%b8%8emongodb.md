---
title: Lift与MongoDB
author: honnix
layout: post
permalink: /archives/645
categories:
  - Lift
  - Scala
  - Technology
tags:
  - Lift
  - MongoDB
  - Scala
---
越来越多的公司和组织掺和到NoSQL运动里来，我也跟风玩玩儿MongoDB。

Lift对MongoDB已经有了很好的支持：http<a title="MongoDB" href="http://www.assembla.com/wiki/show/liftweb/MongoDB" target="_blank">://www.assembla.com/wiki/show/liftweb/MongoDB</a>

下面这段例子是一个非常简单的model设计：

<div class="wp_codebox">
  <table>
    <tr id="p64520">
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
14
15
16
17
18
19
20
21
22
23
24
25
</pre>
      </td>
      
      <td class="code" id="p645code20">
        <pre class="scala" style="font-family:monospace;"><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">class</span></a> ShortenedUrl <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> MongoRecord<span style="color: #F78811;">&#91;</span>ShortenedUrl<span style="color: #F78811;">&#93;</span> <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">with</span></a> MongoId<span style="color: #F78811;">&#91;</span>ShortenedUrl<span style="color: #F78811;">&#93;</span> <span style="color: #F78811;">&#123;</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> meta <span style="color: #000080;">=</span> ShortenedUrl
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> linkId <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> StringField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a>, <span style="color: #F78811;">10</span><span style="color: #F78811;">&#41;</span>
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> originUrl <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> StringField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a>, <span style="color: #F78811;">500</span><span style="color: #F78811;">&#41;</span>
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> shortUrl <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> StringField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a>, <span style="color: #F78811;">100</span><span style="color: #F78811;">&#41;</span>
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> date <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> DateField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a><span style="color: #F78811;">&#41;</span>
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> ip <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> StringField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a>, <span style="color: #F78811;">15</span><span style="color: #F78811;">&#41;</span>
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> clickCount <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> IntField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a><span style="color: #F78811;">&#41;</span>
<span style="color: #F78811;">&#125;</span>
&nbsp;
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> ShortenedUrl <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> ShortenedUrl <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">with</span></a> MongoMetaRecord<span style="color: #F78811;">&#91;</span>ShortenedUrl<span style="color: #F78811;">&#93;</span>
&nbsp;
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">class</span></a> NextId <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> MongoRecord<span style="color: #F78811;">&#91;</span>NextId<span style="color: #F78811;">&#93;</span> <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">with</span></a> MongoId<span style="color: #F78811;">&#91;</span>NextId<span style="color: #F78811;">&#93;</span> <span style="color: #F78811;">&#123;</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> meta <span style="color: #000080;">=</span> NextId
&nbsp;
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> next <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> StringField<span style="color: #F78811;">&#40;</span><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">this</span></a>, <span style="color: #F78811;">10</span><span style="color: #F78811;">&#41;</span>
<span style="color: #F78811;">&#125;</span>
&nbsp;
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> NextId <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> NextId <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">with</span></a> MongoMetaRecord<span style="color: #F78811;">&#91;</span>NextId<span style="color: #F78811;">&#93;</span></pre>
      </td>
    </tr>
  </table>
</div>

这个东西是我仿照<a title="yourls" target="_blank" href="http://yourls.org/">yourls</a>做的数据模型。很简单，两个collection，一个记录缩短域名的所有信息，一个记录下一个URL的id是什么。

查找：

<div class="wp_codebox">
  <table>
    <tr id="p64521">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p645code21">
        <pre class="scala" style="font-family:monospace;">ShortenedUrl.<span style="color: #000000;">find</span><span style="color: #F78811;">&#40;</span>ShortenedUrl.<span style="color: #000000;">linkId</span>.<span style="color: #000000;">name</span> -<span style="color: #000080;">&gt;</span> id<span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

存储：

<div class="wp_codebox">
  <table>
    <tr id="p64522">
      <td class="line_numbers">
        <pre>1
2
</pre>
      </td>
      
      <td class="code" id="p645code22">
        <pre class="scala" style="font-family:monospace;">currentShortenedUrl.<span style="color: #000000;">linkId</span><span style="color: #F78811;">&#40;</span>linkId<span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">shortUrl</span><span style="color: #F78811;">&#40;</span>Props.<span style="color: #000000;">get</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"site"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">open_</span><span style="color: #000080;">!</span> + <span style="color: #6666FF;">"/"</span> + linkId<span style="color: #F78811;">&#41;</span>.
                <span style="color: #000000;">ip</span><span style="color: #F78811;">&#40;</span>containerRequest.<span style="color: #000000;">open_</span><span style="color: #000080;">!</span>.<span style="color: #000000;">remoteAddress</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">clickCount</span><span style="color: #F78811;">&#40;</span><span style="color: #F78811;"></span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">save</span></pre>
      </td>
    </tr>
  </table>
</div>

根本不用描述，太简单了。