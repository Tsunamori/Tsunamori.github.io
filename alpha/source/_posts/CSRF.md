---
title: CSRF
toc: true
date: 2021-04-14 13:43:34
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

## CSRF

* https://kathan19.gitbook.io/howtohunt/csrf/csrf

## CSRF bypass

* -Change Request Method [POST => GET]
* -Remove Total Token Parameter
* -Remove The Token, And Give a Blank Parameter
* -Copy a Unused Valid Token , By Dropping The Request and Use That Token
* -Use Own CSRF Token To Feed it to Victim
* -Replace Value With Of A Token of Same Length
* -Reverse Engineer The Token
* -Extract Token via HTML injection
* -Switch From Non-Form `Content-Type: application/json` or `Content-Type: application/x-url-encoded` To `Content-Type: form-multipart`
* -Change/delete the last or frist character from the token
* -Change referrer to Referrer
* -Bypass the regex
  * If the site is looking for “bank.com” in the referer URL, maybe “bank.com.attacker.com” or “attacker.com/bank.com” will work.
* -Remove the referer header (add this <meta name=”referrer” content=”no-referrer”> in your payload or html code)
* -Clickjacking (https://owasp.org/www-community/attacks/Clickjacking)
  * Exploiting clickjacking on the same endpoint bypasses all CSRF protection. Because technically, the request is indeed originating from the legitimate site. If the page where the vulnerable endpoint is located on is vulnerable to clickjacking, all CSRF protection will be rendered irrelevant and you will be able to achieve the same results as a CSRF   attack on the endpoint, albeit with a bit more effort.
