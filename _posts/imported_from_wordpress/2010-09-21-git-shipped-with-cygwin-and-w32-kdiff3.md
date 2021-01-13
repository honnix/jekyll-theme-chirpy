---
title: Git shipped with Cygwin and W32 KDiff3
author: honnix
layout: post
permalink: /archives/610
categories:
  - Technology
tags:
  - cygwin
  - diff
  - git
  - kdiff3
---
There is a known problem that git shipped with cygwin does not work well with kdiff3.

Merge has no problem at all, but diff does not work. It is strange that git using different approaches for merge and diff.

The reason is that for diff, git will generate temp files in /tmp, and pass file path to kdiff3. Since we’re using W32 version of kdiff3, it definitely does not understand /tmp/xxx. So we have to do some trick to convert /tmp/xxx to a Windows path.

My solution is wrapping kdiff3 with a script, say named kdiff3.sh.

#!/bin/sh

A=\`cygpath -w $6\`  
B=\`cygpath -w $7\`

/cygdrive/c/Program\ Files/KDiff3/kdiff3.exe –auto –L1 “$3″ –L2 “$5″ $A $B

It is quite strait-forward, so I will no explain.

And for git configuration, instead of

git config –global difftool.kdiff3.path “c:/Program Files/KDiff3/kdiff3.exe”

We use

git config –global difftool.kdiff3.path “/path/to/kdiff3.sh”

Not sure how MSysGit does the trick, but it works both for merge and diff quite well.

Maybe I should use MSysGit, but another cygwin env does not sound good for me…

Hope this helps for those guys the same with me.

BTW, can Windows really be used for developing? A system without any tools…