---
title: 认证绕过
toc: true
date: 2021-04-14 11:17:59
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas, 绕过]
---

## 2FA bypass

1. Response Manipulation
  * change the response value of (eg: login) success to 'true' from 'false'
1. Status Code Manipulation
  * change the status code from 4xx to 200 to see if if can bypass
1. 2FA Code Leakage in Response
  * check the response of the 2FA Code Triggering Request to see if the code is leaked
1. JS File Analysis (rare)
  * some JS files may contain info about the 2FA code
1. 2FA Code Reusability
  * same code can be reused
1. Lack of Brute-Force Protection
  * (possible)
1. Missing 2FA Code Integrity Validation
  * Code for any user acc can be used to bypass the 2FA
1. CSRF on 2FA Disabling
  * signup for two account -> Login into attacker account & capture the disable 2FA request -> generate CSRF POC with .HTML extension -> Login into victim account and fire the request — — -> It disable 2FA which leads to 2FA Bypass.
1. Password Reset Disable 2FA
  * 2FA gets disabled on password change/email change
1. Backup Code Abuse
  ***ref: https://medium.com/@surendirans7777/2fa-bypass-techniques-32ec135fb7fe***
1. Clickjacking on 2FA Disabling Page
  * misleading victim to disable the 2FA for the 2FA disabling page
1. Enabling 2FA doesn't expire Previously active Sessions
  * if the previous session is hijacked n app has session timeout vlun
1. Bypass 2FA with null or 000000
  * enter null or 000000 to bypass

## OTP bypass by repeating requests

1. Test
* create an acc with a non-existing ph num
* intercept the request n send to repeater n forward
* change the non-existing num to attacker num, get the OTP n register that non-existing num
  ***ref: https://medium.com/@AGNIHACKERS/otp-bypass-through-response-manipulation-beeb467359d8***

## OTP bypass via response manipulation

1. change any status, false, error, 0, to true, success, 1. And see if it can bypass the OTP
  ***ref: https://medium.com/@MAALP/authentication-bypass-using-response-manipulation-6c33eb1257ac***
