---
layout: post
title: "Homebrew and git svn"
description: "It doesn't work by default"
categories: [Technology]
tags: [Homebrew, git, svn]
---

Just installed subversion and git via Homebrew with default option, `git svn` gave me:

```
Can't locate SVN/Core.pm in @INC (@INC contains: /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/5.16.2/darwin-thread-multi-2level /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/5.16.2 /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl /Library/Perl/5.16/darwin-thread-multi-2level /Library/Perl/5.16 /Network/Library/Perl/5.16/darwin-thread-multi-2level /Network/Library/Perl/5.16 /Library/Perl/Updates/5.16.2 /System/Library/Perl/5.16/darwin-thread-multi-2level /System/Library/Perl/5.16 /System/Library/Perl/Extras/5.16/darwin-thread-multi-2level /System/Library/Perl/Extras/5.16 .) at /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/Git/SVN/Utils.pm line 6.
BEGIN failed--compilation aborted at /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/Git/SVN/Utils.pm line 6.
Compilation failed in require at /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/Git/SVN.pm line 26.
BEGIN failed--compilation aborted at /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/Git/SVN.pm line 33.
Compilation failed in require at /usr/local/Cellar/git/1.8.4.1/libexec/git-core/git-svn line 25.
BEGIN failed--compilation aborted at /usr/local/Cellar/git/1.8.4.1/libexec/git-core/git-svn line 25.
```

Two steps are required to solve this problem:

1. Reinstall subversion with "--perl" as an option

   `brew reinstall subversion --perl`

   But this doesn't work because of [this issue](https://github.com/mxcl/homebrew/issues/20932).
   Apply patch by `brew pull https://github.com/mxcl/homebrew/pull/22668`.

2. Create symbolic links under git installation

   For example in my case:
   
   `ln -s /usr/local/Cellar/subversion/1.8.3/Library/Perl/5.16/darwin-thread-multi-2level/SVN /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/5.16.2/darwin-thread-multi-2level`

   `ln -s /usr/local/Cellar/subversion/1.8.3/Library/Perl/5.16/darwin-thread-multi-2level/auto/SVN /usr/local/Cellar/git/1.8.4.1/lib/perl5/site_perl/5.16.2/darwin-thread-multi-2level/auto`

If you use svn shipped with OS X, you can follow this [article](http://blog.victorquinn.com/fix-git-svn-in-mountain-lion).
