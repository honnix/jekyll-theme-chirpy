---
title: How to Make Embedded Tomcat Support Hot Deploy
author: honnix
layout: post
permalink: /archives/620
categories:
  - Java
  - Technology
tags:
  - embedded tomcat
  - hot deploy
  - LifeCycleListener
---
If you search “embedded tomcat” in google, you will get a bunch of instructions how to do this, but non of them mentioned how to enable hot deploy.

<div class="wp_codebox">
  <table>
    <tr id="p62016">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
</pre>
      </td>
      
      <td class="code" id="p620code16">
        <pre class="java" style="font-family:monospace;">Embedded embedded <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> Embedded<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #666666; font-style: italic;">// create engine, host, connector, context</span>
...
&nbsp;
<span style="color: #006633;">embedded</span>.<span style="color: #006633;">start</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

The above code will not enable hot deploy by default. So what we need to do is adding a LifeCycleListener to the host we create.

<div class="wp_codebox">
  <table>
    <tr id="p62017">
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
</pre>
      </td>
      
      <td class="code" id="p620code17">
        <pre class="java" style="font-family:monospace;">Embedded embedded <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> Embedded<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
...
&nbsp;
<span style="color: #006633;">StandardHost</span> host <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span>StandardHost<span style="color: #009900;">&#41;</span> embedded.<span style="color: #006633;">createHost</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"localhost"</span>,
        getHome<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">+</span> <span style="color: #0000ff;">"/webapps"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
host.<span style="color: #006633;">setAutoDeploy</span><span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">true</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
host.<span style="color: #006633;">setDeployOnStartup</span><span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">true</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
host.<span style="color: #006633;">addLifecycleListener</span><span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">new</span> HostConfig<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
...
<span style="color: #006633;">embedded</span>.<span style="color: #006633;">start</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

And the following things are the same as the stand alone Tomcat: define “.xml” and put war files or directory to “/webapps”.

One more thing about how to deploy the default web app:

*   use ROOT.xml, and ROOT.war
*   or use ROOT.xml and define “docBase” pointing to the war file or directory which should not be under “appBase”, and you can name the war or directory anything you like; if symbolic link is used and the original war file or directory get modified, your web app will also be redeployed