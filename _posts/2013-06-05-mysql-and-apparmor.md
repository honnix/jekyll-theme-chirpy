---
layout: post
title: "MySQL and AppArmor"
description: ""
categories: [Technology]
tags: [MySQL, AppArmor]
---


Started from a crazy idea or a quick and dirty hack more precisely, in order to detect insert/update/delete events within MySQL,
and therefor react on that by executing some system commands,
I found something called [lib_mysqludf_sys](https://github.com/mysqludf/lib_mysqludf_sys) which enables you to execute system
command from within MySQL, and combined with MySQL Trigger I might achieve what I needed.

Everything went fine until I migrated database from my MBP to a Linux workstation. I got __32256__ response after executing
system command and it looked that it was because of permission denied.

Well, then after googling for a while, [AppArmor](http://en.wikipedia.org/wiki/AppArmor) came into my sight. Alright, then
I tried to configure it as following:

```
/tmp/* r,
/tmp/notify.sh ix,
/bin/dash ix,
/usr/bin/curl ix,
```

`notify.sh` is the script I want execute, `/bin/dash` is the actual shell, and `/usr/bin/curl` is the command used in the
script. Generally they should all be here. And the first line actually solves another problem. I tried to import a CSV file
into a table but MySQL always complained about `/tmp/some.csv not found`. It was not because of not found, but AppArmor denied
the access.

It looks that AppArmor is super complex as usual and [this](http://wiki.apparmor.net/index.php/AppArmor_Core_Policy_Reference)
could be a nice reference page.
