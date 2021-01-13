---
layout: post
title: "nginx reverse proxy towards github pages"
description: ""
categories: [Technology]
tags: [nginx, reverse proxy, github, pages]
---


好吧，这是段正常工作的nginx配置：

<pre>
        location / {
                proxy_pass http://honnix.github.com/;
                proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
                proxy_redirect default;
                proxy_buffering off;
                # proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Protocol $scheme;
                proxy_pass_header Set-Cookie;
        }
</pre>
