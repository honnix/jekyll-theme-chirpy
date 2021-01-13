---
title: 给自己建一个Nexus
author: honnix
layout: post
permalink: /archives/701
categories:
  - Java
  - Technology
tags:
  - Nexus
  - repository
  - Sonatype
---
我不是做CM的，但是家里有两台工作机器、一台二奶机，所以弄个Nexus还是有点意义的。

过程相当容易。

1. 这里下：http://nexus.sonatype.org/

2. 解包

3. bin/jsw/<arch>/nexus start（Windows底下不是这个，我不想写，自己研究吧）

4. http://<server>:8081/nexus

5. 在Repositories里面加自己需要的repository，注意三种类型：hosted、proxy、virtual；鼠标移动到问号上有提示，这里就不多说了

6. 修改$HOME/.m2/settings.xml

<div class="wp_codebox">
  <table>
    <tr id="p70139">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
</pre>
      </td>
      
      <td class="code" id="p701code39">
        <pre class="xml" style="font-family:monospace;">    <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;mirror<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>
      <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;id<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>id<span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;/id<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>
      <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;mirrorOf<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>*<span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;/mirrorOf<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>
      <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;name<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>xxx<span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;/name<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>
      <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;url<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>http://<span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;server<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>:8081/nexus/content/groups/public/<span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;/url<span style="color: #000000; font-weight: bold;">&gt;</span></span></span>
    <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;/mirror<span style="color: #000000; font-weight: bold;">&gt;</span></span></span></pre>
      </td>
    </tr>
  </table>
</div>

这里把所有的repository都mirror到Nexus去。

7. 还有复杂的权限控制，我暂时用不到，以后需要再说吧。