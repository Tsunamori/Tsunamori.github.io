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

#### SQLI 绕过
https://www.cnblogs.com/Vinson404/p/7253255.html
https://da4er.top/SQL%E6%B3%A8%E5%85%A5%E7%BB%95%E8%BF%87%E7%AC%94%E8%AE%B0-%E4%B8%80.html
https://www.windylh.com/2018/06/10/Sql%E6%B3%A8%E5%85%A5%E7%BB%95%E8%BF%87%E5%A7%BF%E5%8A%BF/
