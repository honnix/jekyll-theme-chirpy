---
title: Scala与PowerMock
author: honnix
layout: post
permalink: /archives/753
categories:
  - Java
  - Scala
  - Technology
tags:
  - mock
  - mockStatic
  - PowerMock
  - PrepareForTest
  - RunWith
  - Scala
  - ScalaTest
  - Specs
  - 单元测试
---
这个题目对搜索引擎应该是比较友好的，虽然土了些。

<a title="PowerMock" href="http://code.google.com/p/powermock/" target="_blank">PowerMock</a>是个好东西，在某种程度上<a title="单元测试拯救了架构设计？" href="http://honnix.com/blog/archives/747" target="_blank">拯救了架构设计</a>，使得我们不用过分考虑如何用复杂的架构设计来达到可以单元测试的目的。

Java里面如何使用PowerMock就不用多说了，随便google一下满屏幕都是，如果是同事还可以跟我要codekata的材料。但是这事儿换成Scala，情况就完全不同了。

首先要解决的问题是Scala用什么测试框架。比较流行的有<a title="ScalaTest" href="http://www.scalatest.org/" target="_blank">ScalaTest</a>和<a title="Specs" href="http://code.google.com/p/specs/" target="_blank">Specs</a>，二者都支持BDD、ATDD，也就是这个BDD、ATDD把我折磨了一个晚上。

撇开ATDD不谈，我非常想认真尝试一下BDD，但是ScalaTest的BDD需要使用它自己的Runner，Specs的虽然支持JUnit，但是也需要用一个特殊的Runner，这对于PowerMock来说几乎是致命的。PowerMock＋Junit，必须使用PowerMockRunner才能mockStatic，mockNew，PowerMock＋TestNG倒是不需要Runner，但是ScalaTest不支持用TestNG来做BDD，而Specs根本就不支持TestNG。

有点儿混乱，总之一句话，现阶段，Scala＋BDD＋PowerMock这条路完全走不通。如果我的判断有问题，望指正。

那么就退一步，还是JUnit。从一个例子来说明我遭受到的痛苦以及整个解决过程。

<div class="wp_codebox">
  <table>
    <tr id="p75365">
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
      
      <td class="code" id="p753code65">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> MyStatic <span style="color: #009900;">&#123;</span>
    <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <a href="http://www.google.com/search?hl=en&q=allinurl%3Astring+java.sun.com&btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> hello<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #666666; font-style: italic;">// connect to twitter for example</span>
        <span style="color: #000000; font-weight: bold;">return</span> tweet<span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> HelloWorld <span style="color: #009900;">&#123;</span>
    <span style="color: #000000; font-weight: bold;">public</span> <a href="http://www.google.com/search?hl=en&q=allinurl%3Astring+java.sun.com&btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> helloWorld<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #000000; font-weight: bold;">return</span> MyStatic.<span style="color: #006633;">hello</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

上面一段代码，Java＋PowerMock很容易处理：

<div class="wp_codebox">
  <table>
    <tr id="p75366">
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
</pre>
      </td>
      
      <td class="code" id="p753code66">
        <pre class="java" style="font-family:monospace;">@RunWith<span style="color: #009900;">&#40;</span>PowerMockRunner.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span>
@PrepareForTest<span style="color: #009900;">&#40;</span>MyStatic.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span>
<span style="color: #000000; font-weight: bold;">public</span> HelloWorldTest <span style="color: #009900;">&#123;</span>
    @Test
    <span style="color: #000000; font-weight: bold;">public</span> testHelloWorld<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        mockStatic<span style="color: #009900;">&#40;</span>MyStatic.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
        ....
    <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

如果是Scala，问题就接踵而至了。先看功能相似的Scala代码片段：

<div class="wp_codebox">
  <table>
    <tr id="p75367">
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
      
      <td class="code" id="p753code67">
        <pre class="scala" style="font-family:monospace;"><a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">object</span></a> MyStatic <span style="color: #F78811;">&#123;</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> hello <span style="color: #000080;">=</span> <span style="color: #F78811;">&#123;</span>
    <span style="color: #008000; font-style: italic;">// connect to twitter for example</span>
    tweet
  <span style="color: #F78811;">&#125;</span>
<span style="color: #F78811;">&#125;</span>
&nbsp;
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">class</span></a> HelloWorld <span style="color: #F78811;">&#123;</span>
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> helloWorld<span style="color: #F78811;">&#40;</span><span style="color: #F78811;">&#41;</span> <span style="color: #000080;">=</span> MyStatic.<span style="color: #000000;">hello</span>
<span style="color: #F78811;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

@RunWith没有问题：

<div class="wp_codebox">
  <table>
    <tr id="p75368">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p753code68">
        <pre class="scala" style="font-family:monospace;"><span style="color: #000080;">@</span>RunWith<span style="color: #F78811;">&#40;</span>classOf<span style="color: #F78811;">&#91;</span>PowerMockRunner<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

@PrepareForTest就开始出状况了。

首先貌似Scala对annotation的解释有些不太好，不能简单地传一个东西给@PrepareForTest，因为这玩意儿有两个参数，每个都是数组

<div class="wp_codebox">
  <table>
    <tr id="p75369">
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
</pre>
      </td>
      
      <td class="code" id="p753code69">
        <pre class="java" style="font-family:monospace;">@Target<span style="color: #009900;">&#40;</span> <span style="color: #009900;">&#123;</span> ElementType.<span style="color: #006633;">TYPE</span>, ElementType.<span style="color: #006633;">METHOD</span> <span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span>
@Retention<span style="color: #009900;">&#40;</span>RetentionPolicy.<span style="color: #006633;">RUNTIME</span><span style="color: #009900;">&#41;</span>
@Documented
@Inherited
<span style="color: #000000; font-weight: bold;">public</span> @<span style="color: #000000; font-weight: bold;">interface</span> PrepareForTest <span style="color: #009900;">&#123;</span>
    <span style="color: #000000; font-weight: bold;">Class</span><span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span> value<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #000000; font-weight: bold;">default</span> IndicateReloadClass.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #339933;">;</span>
&nbsp;
    <a href="http://www.google.com/search?hl=en&q=allinurl%3Astring+java.sun.com&btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a><span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span> fullyQualifiedNames<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #000000; font-weight: bold;">default</span> <span style="color: #0000ff;">""</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

所以不能：

<div class="wp_codebox">
  <table>
    <tr id="p75370">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p753code70">
        <pre class="scala" style="font-family:monospace;"><span style="color: #000080;">@</span>PrepareForTest<span style="color: #F78811;">&#40;</span>classOf<span style="color: #F78811;">&#91;</span>SomeClass<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

必须要：

<div class="wp_codebox">
  <table>
    <tr id="p75371">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p753code71">
        <pre class="scala" style="font-family:monospace;"><span style="color: #000080;">@</span>PrepareForTest<span style="color: #F78811;">&#40;</span>Array<span style="color: #F78811;">&#40;</span>classOf<span style="color: #F78811;">&#91;</span>SomeClass<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

这个还算是比较容易解决，但是对于Scala里的object，情况又有变化。object不支持classOf这个运算符，classOf[MyStatic]是非法的；MyStatic.getClass倒是可以拿到类，但这个类是另一个，名字是MyStatic$（后面会详细解释这两个类以及它们之间的关系）。但即使getClass得到的类是正确的，也没法直接传给annotation，因为annotation要求参数必须是一个constant，不能是一个方法调用，因此@PrepareForTest(Array(MyStatic.getClass))也是不行的。

怎么办呢？还好@PrepareForTest还有第二个参数，我们可以这样：

<div class="wp_codebox">
  <table>
    <tr id="p75372">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p753code72">
        <pre class="scala" style="font-family:monospace;"><span style="color: #000080;">@</span>PrepareForTest<span style="color: #F78811;">&#40;</span>fullyQualifiedNames <span style="color: #000080;">=</span> Array<span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"com.honnix.test.MyStatic"</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

虽然比较恶心，而且不利于重构，但是聊胜于无吧。

然后就是mockStatic的问题。怎么写呢？

<div class="wp_codebox">
  <table>
    <tr id="p75373">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p753code73">
        <pre class="scala" style="font-family:monospace;">mockStatic<span style="color: #F78811;">&#40;</span>MyStatic.<span style="color: #000000;">getClass</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

这肯定不行，因为之前说了getClass得到的类是MyStatic$。那么怎么样才能mock MyStatic呢？答案就是，反射。

<div class="wp_codebox">
  <table>
    <tr id="p75374">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p753code74">
        <pre class="scala" style="font-family:monospace;">mockStatic<span style="color: #F78811;">&#40;</span>Class.<span style="color: #000000;">forName</span><span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"com.honnix.test.MyStatic"</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

没有其他任何办法得到一个object的class信息，这里有一个很古老的<a title="annotation error" href="https://issues.scala-lang.org/browse/SI-2453" target="_blank">bug</a>。

好吧，两个问题都“解决”了，跑跑看。结果却是我想mock的东西根本就没有被mock。要命了。

事情还得从头说起。Scala对于一个object编译的结果是生成两个类，一个MyStatic，一个MyStatic$：

<div class="wp_codebox">
  <table>
    <tr id="p75375">
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
      
      <td class="code" id="p753code75">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">final</span> <span style="color: #000000; font-weight: bold;">class</span> MyStatic
<span style="color: #009900;">&#123;</span>
  <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000000; font-weight: bold;">final</span> <a href="http://www.google.com/search?hl=en&q=allinurl%3Astring+java.sun.com&btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> hello<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>
  <span style="color: #009900;">&#123;</span>
    <span style="color: #000000; font-weight: bold;">return</span> MyStatic$.<span style="color: #006633;">MODULE</span>$.<span style="color: #006633;">hello</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
  <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

<div class="wp_codebox">
  <table>
    <tr id="p75376">
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
</pre>
      </td>
      
      <td class="code" id="p753code76">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">final</span> <span style="color: #000000; font-weight: bold;">class</span> MyStatic$
  <span style="color: #000000; font-weight: bold;">implements</span> ScalaObject
<span style="color: #009900;">&#123;</span>
  <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000000; font-weight: bold;">final</span>  MODULE$<span style="color: #339933;">;</span>
&nbsp;
  <span style="color: #000000; font-weight: bold;">static</span>
  <span style="color: #009900;">&#123;</span>
    <span style="color: #000000; font-weight: bold;">new</span> <span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
  <span style="color: #009900;">&#125;</span>
&nbsp;
  <span style="color: #000000; font-weight: bold;">public</span> <a href="http://www.google.com/search?hl=en&q=allinurl%3Astring+java.sun.com&btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> hello<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>
  <span style="color: #009900;">&#123;</span>
    <span style="color: #000000; font-weight: bold;">return</span> <span style="color: #0000ff;">"hello"</span><span style="color: #339933;">;</span>
  <span style="color: #009900;">&#125;</span>
&nbsp;
  <span style="color: #000000; font-weight: bold;">private</span> MyStatic$<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>
  <span style="color: #009900;">&#123;</span>
    MODULE$ <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">this</span><span style="color: #339933;">;</span>
  <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

这是反汇编的结果，稍微有点乱，凑合着看吧。不用多解释，代码已经很明白了。

问题是，我对MyStatic进行mock，理论上说也应该可以的，也就是mock之后就无视MyStatic$了。为什么不行呢？还得来看看调用地方的反汇编代码：

<div class="wp_codebox">
  <table>
    <tr id="p75377">
      <td class="line_numbers">
        <pre>1
2
3
4
5
</pre>
      </td>
      
      <td class="code" id="p753code77">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">public</span> testHelloWorld <span style="color: #009900;">&#123;</span>
    ...
    <span style="color: #006633;">MyStatic</span>$.<span style="color: #006633;">MODULE</span>$.<span style="color: #006633;">hello</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
    ...
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

这里面压根儿就没有MyStatic的事儿了！MyStatic被华丽的忽视了！

到此为止终于真相大白了。咱们得这样来mock。

<div class="wp_codebox">
  <table>
    <tr id="p75378">
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
      
      <td class="code" id="p753code78">
        <pre class="scala" style="font-family:monospace;"><span style="color: #000080;">@</span>RunWith<span style="color: #F78811;">&#40;</span>classOf<span style="color: #F78811;">&#91;</span>PowerMockRunner<span style="color: #F78811;">&#93;</span><span style="color: #F78811;">&#41;</span>
<span style="color: #000080;">@</span>PrepareForTest<span style="color: #F78811;">&#40;</span>fullyQualifiedNames <span style="color: #000080;">=</span> Array<span style="color: #F78811;">&#40;</span><span style="color: #6666FF;">"com.honnix.test.MyStatic$"</span><span style="color: #F78811;">&#41;</span><span style="color: #F78811;">&#41;</span>
<a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">class</span></a> HelloWorldTest <span style="color: #F78811;">&#123;</span>
  <span style="color: #000080;">@</span>Test
  <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">def</span></a> testStartAndStop<span style="color: #F78811;">&#40;</span><span style="color: #F78811;">&#41;</span> <span style="color: #F78811;">&#123;</span>
    <a href="http://scala-lang.org"><span style="color: #0000ff; font-weight: bold;">val</span></a> mockMyStatic <span style="color: #000080;">=</span> mock<span style="color: #F78811;">&#40;</span>MyStatic.<span style="color: #000000;">getClass</span><span style="color: #F78811;">&#41;</span>
    Whitebox.<span style="color: #000000;">setInternalState</span><span style="color: #F78811;">&#40;</span>MyStatic.<span style="color: #000000;">getClass</span>, mockMyStatic<span style="color: #F78811;">&#41;</span>
    ...
  <span style="color: #F78811;">&#125;</span>
<span style="color: #F78811;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

想法很简单，把MyStatic$里面的MODULE$给换喽。

代码很丑陋，所以需要强调一下PowerMock应该被用在那些确实不需要复杂的依赖注入的地方来帮助简化架构设计，对于需要做依赖注入的地方，我们还是绝对不能松懈的，不能因为PowerMock啥都能mock就乱写代码。

顺便说一句，Specs对于Mockito已经有很好的支持了，加上Scala那种类似DSL的表达式，用起来很爽。希望PowerMock的作者能考虑支持Scala。