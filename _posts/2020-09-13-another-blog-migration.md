---
layout: post
title: "Another blog migration"
description: ""
category: Technology
tags: [blog, Jekyll]
---

Last I migrated from [WordPress](https://wordpress.org/) to [Jekyll](https://jekyllrb.com/), and I
have a vague memory that it was painful. Now after many years, I had to do another migration/upgrade
because the [template](http://jekyllbootstrap.com/) I built on seems to have been abandoned by the
author.

The migration process was OK ish, but like any migration there were issues.

The vanilla Jekyll looks pretty boring (bad) and misses many features. I ended up picking up a theme
that looks pretty awesome and well maintained (at least for now). The theme is named as Chirpy
and can be found on [GitHub](https://github.com/cotes2020/jekyll-theme-chirpy/). _If you like it,
send the folk a coffee._

I learned that Jekyll sucks when coming to tag and category. Well it is very simple to tag and
categorize a post, but one has to manually create a page for **each** tag/category in order to be
able to see all the posts belonging to the tag/category. On one hand I can understand the rational,
while on the other hand, a built-in implementation
([1](https://github.com/jekyll/jekyll/issues/867),
[2](https://github.com/jekyll/jekyll/issues/6952)) would be very well appreciate. Yes there are
plugins supporting this to some extent but none of them is supported by GitHub pages. The result?
Everyone is customizing a GitHub building/publishing workflow, including Chirpy, more concretely,
which is done by [this
script](https://github.com/cotes2020/jekyll-theme-chirpy/blob/master/_scripts/sh/create_pages.sh).
The problem of the script is plain string processing instead of proper YAML parsing, and that
resulted missing many tags and categories in my case, unfortunately. And because of this, I had to
disable the `Test Site` step in GitHub workflow.
