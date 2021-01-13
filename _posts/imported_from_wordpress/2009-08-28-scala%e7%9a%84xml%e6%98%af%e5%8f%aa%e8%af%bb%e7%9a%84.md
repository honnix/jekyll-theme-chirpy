---
title: Scala的XML是只读的
author: honnix
layout: post
permalink: /archives/422
categories:
  - Scala
  - Technology
tags:
  - BasicTransformer
  - immutable
  - RuleTransformer
  - Scala
  - XML
---
有什么好处呢？最直接的好处就是同一个node可以被多个xml共享，想想DOM是怎么处理这个的。

问题是一个简单的修改就需要重新建立一个完整的xml。Scala提供了BasictTransformer和RuleTransformer，再加上无敌的case匹配，可以方便地处理某类节点。

有个麻烦地地方，Scala-2.7.5的BasicTransformer有bug，只能用nightly或者等2.8发布了。