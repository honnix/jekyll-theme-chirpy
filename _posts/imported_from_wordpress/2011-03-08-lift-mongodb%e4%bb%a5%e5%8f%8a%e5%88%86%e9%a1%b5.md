---
title: Lift, MongoDB以及分页
author: honnix
layout: post
permalink: /archives/654
categories:
  - Lift
  - MongoDB
  - Scala
  - Technology
tags:
  - Lift
  - MongoDB
  - Scala
  - 分页
---
说来惭愧，从来没写过分页的代码，知道是怎么回事儿，但没干过总归差了这么一点儿。

MongoDB和传统的数据库类似，提供了skip和limit，前者用来跳过一批记录，后者用来选择多少条。怎么用这里有很详细的解释：<a title="Advanced Query" href="http://www.mongodb.org/display/DOCS/Advanced+Queries" target="_blank">http://www.mongodb.org/display/DOCS/Advanced+Queries</a>，就不多说了。重点讨论Lift里面怎么用。

分页通常有这么几个条件：排序字段、排序方式（升序还是降序）、每页显示条数、当前页面。这些参数可以通过URL传递，譬如：sort-by=linkId&sort-order=-1&perpage=3&page=2，然后HTTP GET发送。

对于这种per request的参数，当然可以用的时候直接从著名的“S”里面取，也可以用RequestVar：

<div class="wp_codebox">
  <table>
    <tr id="p65423">
      <td class="line_numbers">
        <pre>1
2
3
4
5
</pre>
      </td>
      
      <td class="code" id="p654code23">
        <pre class="scala" style="font-family:monospace;"><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> page <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>Int<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"page"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"1"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">toInt</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> perpage <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>Int<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"perpage"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"10"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">toInt</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> offset <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>Int<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span><span style="color: #F78811;">&#40;</span>page - <span style="color: #F78811;">1</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">*</span> perpage<span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> sortBy <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>String<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"sort-by"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span>ShortenedUrl.<span style="color: #000000;">linkId</span>.<span style="color: #000000;">name</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> sortOrder <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>Int<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"sort-order"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"-1"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">toInt</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

这其中offset表示当前页的起始条目，-1表示降序，默认排序字段为linkId。

只有这些还不过瘾，我们再加上搜索条件：

1.  关键字
2.  点击次数

<div class="wp_codebox">
  <table>
    <tr id="p65424">
      <td class="line_numbers">
        <pre>1
2
3
4
</pre>
      </td>
      
      <td class="code" id="p654code24">
        <pre class="scala" style="font-family:monospace;"><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> search <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>String<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"search"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">""</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> searchIn <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>String<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"search-in"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span>ShortenedUrl.<span style="color: #000000;">originUrl</span>.<span style="color: #000000;">name</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> clickFilter <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>String<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"click-filter"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"gte"</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> clickLimit <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>String<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>S.<span style="color: #000000;">param</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"click-limit"</span><span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">openOr</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">""</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

默认搜索originUrl，点击次数大于等于某个值。

有过数据库搜索设计经验的人一般都会这么干：

<div class="wp_codebox">
  <table>
    <tr id="p65425">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p654code25">
        <pre class="sql" style="font-family:monospace;"><span style="color: #993333; font-weight: bold;">select</span> <span style="color: #66cc66;">*</span> <span style="color: #993333; font-weight: bold;">from</span> <span style="color: #993333; font-weight: bold;">table</span> <span style="color: #993333; font-weight: bold;">where</span> <span style="color: #cc66cc;">1</span><span style="color: #66cc66;">=</span><span style="color: #cc66cc;">1</span></pre>
      </td>
    </tr>
  </table>
</div>

然后在后面拼搜索条件：

<div class="wp_codebox">
  <table>
    <tr id="p65426">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p654code26">
        <pre class="sql" style="font-family:monospace;"><span style="color: #993333; font-weight: bold;">and</span> originUrl <span style="color: #993333; font-weight: bold;">like</span> <span style="color: #ff0000;">'%google%'</span> <span style="color: #993333; font-weight: bold;">and</span> clickCount <span style="color: #66cc66;">&gt;=</span> <span style="color: #cc66cc;">10</span> sort <span style="color: #993333; font-weight: bold;">by</span> linkId skip <span style="color: #cc66cc;">2</span> <span style="color: #993333; font-weight: bold;">limit</span> <span style="color: #cc66cc;">10</span></pre>
      </td>
    </tr>
  </table>
</div>

我们如法炮制，不过稍微改变一下：

<div class="wp_codebox">
  <table>
    <tr id="p65427">
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
</pre>
      </td>
      
      <td class="code" id="p654code27">
        <pre class="scala" style="font-family:monospace;"><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> clickObject <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>JObject<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>
    <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">if</span></a> <span style="color: #F78811;">&#40;</span>clickLimit.<span style="color: #000000;">isEmpty</span><span style="color: #F78811;">&#41;</span> JObject<span style="color: #F78811;">&#40;</span>Nil<span style="color: #F78811;">&#41;</span>
    <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">else</span></a> <span style="color: #F78811;">&#40;</span>ShortenedUrl.<span style="color: #000000;">clickCount</span>.<span style="color: #000000;">name</span> -<span style="color: #000080;">&gt;</span> <span style="color: #F78811;">&#40;</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"$"</span> + clickFilter<span style="color: #F78811;">&#41;</span> -<span style="color: #000080;">&gt;</span> clickLimit.<span style="color: #000000;">is</span>.<span style="color: #000000;">toInt</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<span style="color: #F78811;">&#41;</span>
&nbsp;
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> searchObject <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>JObject<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">if</span></a> <span style="color: #F78811;">&#40;</span>search.<span style="color: #000000;">isEmpty</span><span style="color: #F78811;">&#41;</span> JObject<span style="color: #F78811;">&#40;</span>Nil<span style="color: #F78811;">&#41;</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">else</span></a> <span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"$where"</span> -<span style="color: #000080;">&gt;</span> <span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"this."</span> + searchIn + <span style="color: #6666FF;">".indexOf('"</span> + search + <span style="color: #6666FF;">"') != -1"</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<span style="color: #F78811;">&#41;</span>
&nbsp;
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> findOptions <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">extends</span></a> RequestVar<span style="color: #F78811;">&#91;</span>List<span style="color: #F78811;">&#91;</span>FindOption<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#40;</span>
  List<span style="color: #F78811;">&#40;</span>Skip<span style="color: #F78811;">&#40;</span>offset<span style="color: #F78811;">&#41;</span>, Limit<span style="color: #F78811;">&#40;</span>perpage<span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<span style="color: #F78811;">&#41;</span>
&nbsp;
shortenedUrl.<span style="color: #000000;">findAll</span><span style="color: #F78811;">&#40;</span>clickObject.<span style="color: #000000;">is</span> ~ searchObject.<span style="color: #000000;">is</span>,
    <span style="color: #F78811;">&#40;</span>sortBy.<span style="color: #000000;">is</span> -<span style="color: #000080;">&gt;</span> sortOrder.<span style="color: #000000;">is</span><span style="color: #F78811;">&#41;</span>, findOptions<span style="color: #000080;">:</span> <span style="color: #000080;">_*</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

如果没有搜索条件，返回Nil表示空的List；否则(x -> y)构建一个JObject（这里是Scala的语法糖衣，背后发生了很多）；$where那里纯粹是MongoDB特有的，根本就是js；Skip和Limit跟SQL的含义完全相同。这样对最后一个statement的解读也就很自然了。

数据的事情搞定了，剩下就是页面处理，Lift再次表示了自己的牛逼：

<div class="wp_codebox">
  <table>
    <tr id="p65428">
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
      
      <td class="code" id="p654code28">
        <pre class="scala" style="font-family:monospace;">    <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> generateNav<span style="color: #000080;">:</span> NodeSeq <span style="color: #000080;">=</span> <span style="color: #F78811;">&#123;</span>
      <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> generateHref<span style="color: #F78811;">&#40;</span>page<span style="color: #000080;">:</span> Int<span style="color: #F78811;">&#41;</span> <span style="color: #000080;">=</span> <span style="color: #6666FF;">"/?search="</span> + search + <span style="color: #6666FF;">"&amp;sort-by="</span> + sortBy + <span style="color: #6666FF;">"&amp;sort-order="</span> + sortOrder +
              <span style="color: #6666FF;">"&amp;search-in="</span> + searchIn + <span style="color: #6666FF;">"&amp;click-filter="</span> + clickFilter + <span style="color: #6666FF;">"&amp;click-limit="</span> + clickLimit +
              <span style="color: #6666FF;">"&amp;perpage="</span> + perpage + <span style="color: #6666FF;">"&amp;page="</span> + page
&nbsp;
      <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> left <span style="color: #000080;">=</span> <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">if</span></a> <span style="color: #F78811;">&#40;</span>page.<span style="color: #000000;">is</span> <span style="color: #000080;">!=</span> <span style="color: #F78811;">1</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">&lt;</span>a title<span style="color: #000080;">=</span><span style="color: #6666FF;">"{&quot;«"</span> href<span style="color: #000080;">=</span><span style="color: #6666FF;">"{generateHref(page"</span><span style="color: #000080;">&gt;</span>«<span style="color: #000080;">&lt;</span>/a<span style="color: #000080;">&gt;</span>
      <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">else</span></a> Nil
&nbsp;
      <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> pages <span style="color: #000080;">=</span> <span style="color: #F78811;">&#40;</span><span style="color: #F78811;">1</span> to totalPages<span style="color: #F78811;">&#41;</span>.<span style="color: #000000;">toList</span>.<span style="color: #000000;">map</span> <span style="color: #F78811;">&#123;</span>
        x <span style="color: #000080;">=&gt;</span>
          <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">if</span></a> <span style="color: #F78811;">&#40;</span>x <span style="color: #000080;">==</span> page.<span style="color: #000000;">is</span><span style="color: #F78811;">&#41;</span>
            <span style="color: #000080;">&lt;</span>strong<span style="color: #000080;">&gt;</span>
              <span style="color: #F78811;">&#123;</span><span style="color: #6666FF;">"["</span> + x + <span style="color: #6666FF;">"]"</span><span style="color: #F78811;">&#125;</span>
            <span style="color: #000080;">&lt;</span>/strong<span style="color: #000080;">&gt;</span>
          <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">else</span></a>
            <span style="color: #000080;">&lt;</span>a title<span style="color: #000080;">=</span><span style="color: #6666FF;">"{&quot;Page"</span> href<span style="color: #000080;">=</span><span style="color: #6666FF;">"{generateHref(x)}"</span><span style="color: #000080;">&gt;</span>
              <span style="color: #F78811;">&#123;</span>x<span style="color: #F78811;">&#125;</span>
            <span style="color: #000080;">&lt;</span>/a<span style="color: #000080;">&gt;</span>
      <span style="color: #F78811;">&#125;</span>
&nbsp;
      <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> right <span style="color: #000080;">=</span> <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">if</span></a> <span style="color: #F78811;">&#40;</span>page.<span style="color: #000000;">is</span> <span style="color: #000080;">!=</span> totalPages<span style="color: #F78811;">&#41;</span> <span style="color: #000080;">&lt;</span>a title<span style="color: #000080;">=</span><span style="color: #6666FF;">"{&quot;Go"</span> href<span style="color: #000080;">=</span><span style="color: #6666FF;">"{generateHref(page.is"</span><span style="color: #000080;">&gt;</span>»<span style="color: #000080;">&lt;</span>/a<span style="color: #000080;">&gt;</span>
      <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">else</span></a> Nil
&nbsp;
      left ++ pages ++ right
    <span style="color: #F78811;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

我写得比较烂，也就这么几行。效果就是这样：<a href="/assets/img//imported_from_wordpress/2011/03/Screen-shot-2011-03-08-at-10.37.08-PM.png" rel="lightbox[654]"><img class="size-full wp-image-657" title="paging" src="/assets/img//imported_from_wordpress/2011/03/Screen-shot-2011-03-08-at-10.37.08-PM.png" alt="" width="164" height="21" /></a>。

 