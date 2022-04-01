---
title: 参数污染
toc: true
date: 2021-04-14 14:13:48
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. Parameter pollution in social sharing buttons
    * find a social sharing button n get the sharing link.
    * modified it from `https://taget.com/how-to-hunt ` to such as `https://taget.com/how-to-hunt?&u=https://attacker.com/vaya&text=another_site:https://attacker.com/vaya`
    * click the share button n see if the attack website is in the sharing content
