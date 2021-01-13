---
title: 稍显诡异的MongoDB Collection
author: honnix
layout: post
permalink: /archives/673
categories:
  - MongoDB
  - Technology
tags:
  - collection
  - exist
  - MongoDB
  - system.namespaces
---
今天给同事演示的时候露怯了，直接飞出个异常。

对一个不存在的collection，find，count之类的操作都没问题，但是直接mapReduce就完蛋了，告诉我“ns doesn’t exist”。

解决办法有两个：

1.  查询db的system.namespaces，这样最可靠
2.  直接count，如果是0，可以当作没有来处理；不过这个要看业务逻辑，因为collection里面没有数据的时候也会返回0

有人提了新加几个接口的需求，不知道什么时候会做进去：<a href="http://jira.mongodb.org/browse/SERVER-1938" target="_blank">http://jira.mongodb.org/browse/SERVER-1938</a>。