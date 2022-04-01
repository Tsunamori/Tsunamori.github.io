---
title: 未闭环的认证session管理
toc: true
date: 2021-04-14 11:35:41
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

## Session based bugs

1. Old session does not expire
  * log in the account through two browser(eg. firefox n chrome)
  * change pwd or reset pwd on one browser, then refresh the other browser to see if still logged in
1. Session hijacking (intended behavior)
  * login the acc
  * copy all cookies n logout
  * paste the cookies n refresh
1.  Password reset link token not expire (insecure configurability)
  * create acc, request a forget pwd link
  * don't use it, instead, change the email to new one
  * now use the forget pwd link to sent info to the old email n see if pwd is able to change
1. Server security misconfiguration
  * login, play around n logout, goback one page, see if acc is logged in or can view pages limits to the user
