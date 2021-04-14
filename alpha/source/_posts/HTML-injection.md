---
title: HTML-injection
toc: true
date: 2021-04-14 14:03:35
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. Password reset links are usually addressed to your account name followed by the reset link. Also if the application allows you to have your account name with tags and special characters then you should try this.
1. steps
    * Create ur acc, edit the name to`<h1>attacker</h1>` or `"abc><h1>attacker</h1>`
    and save it.
    * request a password reset n check the reset email.
    * see if the `<h1>` tag executed.
1. tips
    * HTML injection are usually considered as low to medium severity bugs but you can escalate the severity by serving a malicious link by using `<a href>` for eg:
    `<h1>attacker</h1><a href="your-controlled-domain"Click here</a>`

    * You can redirect the user to your malicious domain and serve a fake reset password page to steal credentials Also you can serve a previously found XSS page and steal user cookies etc.
