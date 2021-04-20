---
title: SQLI
toc: true
date: 2021-04-14 14:17:04
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. https://kathan19.gitbook.io/howtohunt/sqli/sql_injection
1. Cookie injection: can only works for sqlmap above level 2. *eg:sqlmap --cookie="hint=id; id=1" -u "http://" -p id --level=2*
1. Any other parameters needs to be forced, such as UA injection: user-agent=blabla* (--level 2)
1. SQL injection through HTTP headers: https://resources.infosecinstitute.com/topic/sql-injection-http-headers/ (level 2 recommended as tried)
1. Time-based blind injection: use thread=blabla to fasten it.
    *  https://www.freebuf.com/sectool/222967.html
    *  https://zhuanlan.zhihu.com/p/129904025
    *  https://zhuanlan.kanxue.com/article-10333.htm
