---
title: 如何在Lift里用javascript的confirm
author: honnix
layout: post
permalink: /archives/566
categories:
  - Lift
  - Scala
  - Technology
tags:
  - ajax
  - confirm
  - Javascript
  - Lift
  - Scala
---
使用Lift的SHtml.a()设计ajax调用的时候，老版本的Lift不支持给onclick加入用户自己定义的javascript方法，新版本的有了。

<div class="wp_codebox">
  <table>
    <tr id="p56610">
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
      
      <td class="code" id="p566code10">
        <pre class="scala" style="font-family:monospace;"><span style="color: #00ff00; font-style: italic;">/**
   * Create an anchor tag around a body which will do an AJAX call and invoke the function
   *
   * @param jsFunc -- the user function that will be executed. This function will receive as last parameter
   *                  the function that will actually do the ajax call. Hence the user function can decide when
   * 				  to make the ajax request.
   * @param func - the function to invoke when the link is clicked
   * @param body - the NodeSeq to wrap in the anchor tag
   * @param attrs - the anchor node attributes
   */</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> a<span style="color: #F78811;">&#40;</span>jsFunc<span style="color: #000080;">:</span> Call, func<span style="color: #000080;">:</span> <span style="color: #F78811;">&#40;</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">=&gt;;</span> JsCmd, body<span style="color: #000080;">:</span> NodeSeq, attrs<span style="color: #000080;">:</span> <span style="color: #F78811;">&#40;</span>String, String<span style="color: #F78811;">&#41;</span><span style="color: #000080;">*</span><span style="color: #F78811;">&#41;</span><span style="color: #000080;">:</span> Elem <span style="color: #000080;">=</span> <span style="color: #F78811;">&#123;</span>
    attrs.<span style="color: #000000;">foldLeft</span><span style="color: #F78811;">&#40;</span>fmapFunc<span style="color: #F78811;">&#40;</span>contextFuncBuilder<span style="color: #F78811;">&#40;</span>func<span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#40;</span>name <span style="color: #000080;">=&gt;;</span>
            <span style="color: #000080;">&lt;</span>a onclick<span style="color: #000080;">=</span><span style="color: #6666FF;">"{deferCall(Str(name"</span> href<span style="color: #000080;">=</span><span style="color: #6666FF;">"javascript://"</span><span style="color: #000080;">&gt;</span><span style="color: #F78811;">&#123;</span>body<span style="color: #F78811;">&#125;</span><span style="color: #000080;">&lt;</span>/a<span style="color: #000080;">&gt;</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#40;</span><span style="color: #000080;">_</span> <span style="color: #000080;">%</span> <span style="color: #000080;">_</span><span style="color: #F78811;">&#41;</span>
  <span style="color: #F78811;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

对于jsFunc可以这样理解：  
用户定义了一个方法：

<div class="wp_codebox">
  <table>
    <tr id="p56611">
      <td class="line_numbers">
        <pre>1
2
3
4
</pre>
      </td>
      
      <td class="code" id="p566code11">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #003366; font-weight: bold;">function</span> <span style="color: #000066; font-weight: bold;">delete</span><span style="color: #009900;">&#40;</span>toDelete<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
  <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #000066;">confirm</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"Delete?"</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span>
    toDelete<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

那么jsFunc可以这样定义：Call(“delete”)，Lift会把它生成的ajax方法作为最后一个参数传递给delete方法。
