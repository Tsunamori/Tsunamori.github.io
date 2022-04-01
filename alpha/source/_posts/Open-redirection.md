---
title: 重定向
toc: true
date: 2021-04-14 14:12:51
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. find OpenRedirect trick
    * steps
        * create an acc, record the user profile link, logout n clean all the cookies.
        * paste n visit the profile link, it may redirect u to the login like `https://samplesite.me/login?next=accounts/profile` or `https://samplesite.me/login?retUrl=accounts/profile`
        * try to exploit the parameter by adding an external domain eg: `https://samplesite.me/login?next=https://evil.com/` or `https://samplesite.me/login?next=https://samplesite.me@evil.com/ #`or `https://targetweb.com?url=www.targetweb.com.attackersite.com (with created subdomain)` (to beat the bad regex filter)
        * else, try the XSS eg: ` https://samplesite.me/login?next=javascript:alert(1);//`
