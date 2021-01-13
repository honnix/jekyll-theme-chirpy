---
layout: post
title: "Nutch and Anchor"
description: "how to configure nutch to store anchor text and index it into solr"
categories: [Technology]
tags: [Nutch, Anchor, Solr, Index]
---


Nutch is cool and pretty simple to start. Lots of tutorials over the Internet.

The problem I had was I couldn't make nutch to index or even store anchor text. No argue
but I just needed it or them.

Now here is the twist.

* Modify `$NUTCH_HOME/conf/solrindex-mapping.xml` adding `<field dest="anchor" source="anchor"/>`
* Either modify `$NUTCH_HOME/conf/nutch-default.xml` changing `db.ignore.internal.links` to `false`
  or overwrite it in `$NUTCH_HOME/conf/nutch-site.xml`

This simple but it indeed took me sometime to figure out.
