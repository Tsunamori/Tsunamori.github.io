---
title: WP-BUUCTF-Web
toc: true
date: 2021-05-08 11:43:54
tags: [CTF, WriteUp]
categories: [100 Cyber security, 120 CTF, 121 Web ]
---

#### [极客大挑战 2019]EasySQL 1
上工具，收获一个彩蛋：(with --string="用 sqlmap 是没有灵魂的")，淦，被预判了。
又到了每次打手注都要看看自己笔记的时刻，直接上报错注入看下。淦，注入操作被发现了。
那就过burp看过滤了哪些函数。
过滤了incert、select。
