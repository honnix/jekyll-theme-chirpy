---
title: 关于Lift的CSS Binding
author: honnix
layout: post
permalink: /archives/637
categories:
  - Lift
  - Scala
  - Technology
tags:
  - CSS Binding
  - Lift
  - Scala
  - TemplateFinder
---
具体怎么回事儿，这里写得很清楚，就不啰嗦了：<a title="CSS Binding" href="http://www.assembla.com/wiki/show/liftweb/Binding_via_CSS_Selectors" target="_blank">http://www.assembla.com/wiki/show/liftweb/Binding_via_CSS_Selectors</a>。

有一点需要特别说明一下：为了彻底把页面设计和后台代码分离，我希望所有和页面布局展示的地方都用静态html实现，然后在snippet里面作binding。这时候就需要TemplateFinder了。

看下面这段：

<div class="wp_codebox">
  <table>
    <tr id="p63719">
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
</pre>
      </td>
      
      <td class="code" id="p637code19">
        <pre class="scala" style="font-family:monospace;">TemplateFinder.<span style="color: #000000;">findAnyTemplate</span><span style="color: #F78811;">&#40;</span>List<span style="color: #F78811;">&#40;</span>TemplatesHidden, <span style="color: #6666FF;">"edit"</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span> map <span style="color: #F78811;">&#123;</span>
  <span style="color: #6666FF;">"#real-content ^^"</span><span style="color: #F78811;">&#41;</span><span style="color: #000080;">#&gt;</span> <span style="color: #6666FF;">"true"</span> andThen
    <span style="color: #6666FF;">"#real-conent [id]"</span> <span style="color: #000080;">#&gt;</span> <span style="color: #F78811;">&#40;</span>EditPrefix + shortenedUrl.<span style="color: #000000;">linkId</span>.<span style="color: #000000;">value</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#edit-url [name]"</span> <span style="color: #000080;">#&gt;</span> urlId <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#edit-url [value]"</span> <span style="color: #000080;">#&gt;</span> shortenedUrl.<span style="color: #000000;">originUrl</span>.<span style="color: #000000;">value</span> <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#edit-url [id]"</span> <span style="color: #000080;">#&gt;;</span> urlId <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#save-button [onclick]"</span> <span style="color: #000080;">#&</span>gt<span style="color: #000080;">;</span> js.<span style="color: #000000;">toJsCmd</span> <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#save-button [id]"</span> <span style="color: #000080;">#&gt;</span> <span style="color: #F78811;">&#40;</span>EditPrefix + <span style="color: #6666FF;">"submit-"</span> + shortenedUrl.<span style="color: #000000;">linkId</span>.<span style="color: #000000;">value</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#cancel-button [onclick]"</span> <span style="color: #000080;">#&gt;</span> <span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"hide_edit('"</span> + shortenedUrl.<span style="color: #000000;">linkId</span>.<span style="color: #000000;">value</span> + <span style="color: #6666FF;">"')"</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">&</span>
    <span style="color: #6666FF;">"#cancel-button [id]"</span> <span style="color: #000080;">#&gt;</span> <span style="color: #F78811;">&#40;</span>EditPrefix + <span style="color: #6666FF;">"close-"</span> + shortenedUrl.<span style="color: #000000;">linkId</span>.<span style="color: #000000;">value</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

edit.html是一个完整的静态html，用来显示一个table，上面代码做的事情就是选中id为real-content的这个节点，然后对它做CSS的binding以实现动态页面。