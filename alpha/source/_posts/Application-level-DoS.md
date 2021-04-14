---
title: Application level DoS
toc: true
date: 2021-04-14 11:06:52
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

## Email bounce issues

1. https://infosecwriteups.com/an-unexpected-bounty-email-bounce-issues-b9f24a35eb68

## Long password DoS

1. Test
    * Test in Forgot Password Page n Change Password, cuz the registration usually have length limit but others don't.
    * Use a password around 150-200 words to check if there has length limit. Then choose a longer password n keep checking the response time, to see if the app crashes for few seconds.
    * reason: The long password will cause a problem while hashing it for storing in dataset.

## Long string DoS

1. Test
    * Create app and put field like username or address or even profile picture name parameter (second reference) like 1000 character of string .
    * Search A's account from B's account either it will either keeping searching for long time or crash(500 Error code).
  ***ref: https://shahjerry33.medium.com/long-string-dos-6ba8ceab3aa0***
  ***https://hackerone.com/reports/764434***
