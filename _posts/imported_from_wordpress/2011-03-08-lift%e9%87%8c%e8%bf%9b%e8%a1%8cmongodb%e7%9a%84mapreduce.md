---
title: Lift里进行MongoDB的MapReduce
author: honnix
layout: post
permalink: /archives/659
categories:
  - Lift
  - MongoDB
  - Scala
  - Technology
tags:
  - Lift
  - MapReduce
  - MongoDB
  - Scala
---
其实说Lift里不够准确，Lift对MapReduce没做什么封装，基本上就是直接调用mongo-java-driver的API。

举例说明，计算所有URL的点击次数：

<div class="wp_codebox">
  <table>
    <tr id="p65929">
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
</pre>
      </td>
      
      <td class="code" id="p659code29">
        <pre class="scala" style="font-family:monospace;">    <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> countClicks <span style="color: #000080;">=</span> <span style="color: #F78811;">&#123;</span>
      MongoDB.<span style="color: #000000;">useCollection</span><span style="color: #F78811;">&#40;</span>shortenedUrl.<span style="color: #000000;">collectionName</span><span style="color: #F78811;">&#41;</span> <span style="color: #F78811;">&#123;</span>
        x <span style="color: #000080;">=&</span>gt<span style="color: #000080;">;</span> <span style="color: #F78811;">&#123;</span>
          <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> map <span style="color: #000080;">=</span> <span style="color: #6666FF;">""</span><span style="color: #6666FF;">"function() { emit("</span>totalClickCount<span style="color: #6666FF;">", this.%s); }"</span><span style="color: #6666FF;">""</span> format ShortenedUrl.<span style="color: #000000;">clickCount</span>.<span style="color: #000000;">name</span>
          <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> reduce <span style="color: #000080;">=</span> <span style="color: #6666FF;">""</span><span style="color: #6666FF;">"function(key, values) { return Array.sum(values); }"</span><span style="color: #6666FF;">""</span>
          <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> results <span style="color: #000080;">=</span> x.<span style="color: #000000;">mapReduce</span><span style="color: #F78811;">&#40;</span>map, reduce, <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">null</span></a>, <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">null</span></a><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">results</span>
&nbsp;
          <span style="color: #00ff00; font-style: italic;">/**
           * all numbers returned by mongodb is Double since this is how number defined by javascript
           */</span>
          <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">if</span></a> <span style="color: #F78811;">&#40;</span>results.<span style="color: #000000;">hasNext</span><span style="color: #F78811;">&#41;</span> results.<span style="color: #000000;">next</span>.<span style="color: #000000;">get</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"value"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">asInstanceOf</span><span style="color: #F78811;">&#91;</span>Number<span style="color: #F78811;">&#93;</span>.<span style="color: #000000;">intValue</span>.<span style="color: #000000;">toString</span> <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">else</span></a> <span style="color: #6666FF;">"0"</span>
        <span style="color: #F78811;">&#125;</span>
      <span style="color: #F78811;">&#125;</span>
    <span style="color: #F78811;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

map的时候把this.clickCount的值塞给”totalClickCount”这个key，reduce把所有这些值加起来，MongoDB会生成一个临时的collection，从里面选择“value”这个字段就可以了。

需要注意的是所有返回的数值类型都是Double，虽然BSON里对各种类型都有定义，但是目前为止MongoDB只支持返回Double。对js了解的人可能会比较清楚，但我就惨了。调了很长时间，一个bit一个bit的看，甚至还去MongoDB的JIRA上问：<a href="http://jira.mongodb.org/browse/SERVER-2688" target="_blank">http://jira.mongodb.org/browse/SERVER-2688</a>。土死了……