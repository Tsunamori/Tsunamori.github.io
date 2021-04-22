---
title: SSRF
toc: true
date: 2021-04-14 14:20:24
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. SSRF https://kathan19.gitbook.io/howtohunt/ssrf/ssrf
1. blind SSRF https://kathan19.gitbook.io/howtohunt/ssrf/blind_ssrf
1. SSRF -> getshell https://xz.aliyun.com/t/9371
1. SSRF伪协议： `file:///,gopher://,ftp://`
    * ref:
        * https://www.cnblogs.com/-mo-/p/11673190.html
        * https://xz.aliyun.com/t/7405#toc-1
        * https://www.secpulse.com/archives/70471.html
    * dict://127.0.0.1:22/info 探测端口/redis等服务 ref:https://3wapp.github.io/WebSecurity/%E5%B8%B8%E8%A7%81%E5%8D%8F%E8%AE%AE.html
    * gopher://fuzz.wuyun.org:8080/gopher https://xz.aliyun.com/t/6993
    * file:///etc/passwd, file:///var/www/html/flag.php
    * 例题：
        * https://www.cnblogs.com/wangtanzhi/p/11900128.html
        * https://times0ng.github.io/2019/02/14/%E7%AC%AC%E5%8D%81%E5%9B%9B%E7%AF%87%EF%BC%9ASSRF%E8%AF%BB%E5%8F%96%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6/
1.  redis SSRF: https://www.yuque.com/tianxiadamutou/od438i/ud5pu8?language=en-us
