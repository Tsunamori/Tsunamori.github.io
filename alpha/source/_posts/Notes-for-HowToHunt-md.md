---
title: Notes for HowToHunt.md
toc: true
date: 2021-04-08 16:05:57
categories: [Cyber security, Bug bounty]
tag: Notes
---

## Account takeover

1. Have a session hijacking -> Look for xss to stole the cookies n expand the impact
1. The target has weak password policy -> Create a (very) weak password n brute force this account to show the POC
1. Password reset poisoning:
    * Go to password reset function -> Enter email n intercept
    * Change host header to some other host i.e,
          * Host:target.com
           * Host:attacker.com
also try to add some headers without changing host like
          * X-Forwarded-Host: evil.com
           * Referrer: https ://evil.com
    * Forward this if you found that in next request attacker.com means you successfully theft the token.:)
1. Using CSRF:
    * Change Password function
    * Email change
    * Change Security Question
1. Use git recon or google recon to find sensitive data (depends on luck :))
1. Token leaks in response:
    * For registration:
        * Intercept the signup request that contains data you have entered.
        * Click on action -> do -> intercept response to this request.
        * Click forward.
        * Check response it that contains any link,any token or otp(one-time password).
    * For password reset:
        * Intercept the forget password option.
        * Click on action -> do -> intercept response to this request.
        * Click forward.
        * Check response it that contains any link,any token or otp.

## Application level DoS

1. Email bounce issues:
          * ref: https://infosecwriteups.com/an-unexpected-bounty-email-bounce-issues-b9f24a35eb68
1. Long password DoS:
    * Test in Forgot Password Page n Change Password, cuz the registration usually have length limit but others don't.
    * Use a password around 150-200 words to check if there has length limit. Then choose a longer password n keep checking the response time, to see if the app crashes for few seconds.
    * reason: The long password will cause a problem while hashing it for storing in dataset.
1. Long string DoS:
    * Create app and put field like username or address or even profile picture name parameter (second reference) like 1000 character of string .
    * Search A's account from B's account either it will either keeping searching for long time or crash(500 Error code).
          * ref: https://shahjerry33.medium.com/long-string-dos-6ba8ceab3aa0
                  https://hackerone.com/reports/764434

## Authentication bypass

1. 2FA bypass
    * Response Manipulation
      * change the response value of (eg: login) success to 'true' from 'false'
    * Status Code Manipulation
      * change the status code from 4xx to 200 to see if if can bypass
    * 2FA Code Leakage in Response
      * check the response of the 2FA Code Triggering Request to see if the code is leaked
    * JS File Analysis (rare)
      * some JS files may contain info about the 2FA code
    * 2FA Code Reusability
      * same code can be reused
    * Lack of Brute-Force Protection
      * (possible)
    * Missing 2FA Code Integrity Validation
      * Code for any user acc can be used to bypass the 2FA
    * CSRF on 2FA Disabling
      * signup for two account -> Login into attacker account & capture the disable 2FA request -> generate CSRF POC with .HTML extension -> Login into victim account and fire the request — — -> It disable 2FA which leads to 2FA Bypass.
    * Password Reset Disable 2FA
      * 2FA gets disabled on password change/email change
    * Backup Code Abuse
          * ref: https://medium.com/@surendirans7777/2fa-bypass-techniques-32ec135fb7fe
    * Clickjacking on 2FA Disabling Page
      * misleading victim to disable the 2FA for the 2FA disabling page
    * Enabling 2FA doesn't expire Previously active Sessions
      * if the previous session is hijacked n app has session timeout vlun
    * Bypass 2FA with null or 000000
      * enter null or 000000 to bypass
1. OTP bypass by repeating requests
    * create an acc with a non-existing ph num
    * intercept the request n send to repeater n forward
    * change the non-existing num to attacker num, get the OTP n register that non-existing num
          * ref: https://medium.com/@AGNIHACKERS/otp-bypass-through-response-manipulation-beeb467359d8
1. OTP bypass via response manipulation
    * change any status, false, error, 0, to true, success, 1. And see if it can bypass the OTP
          * ref: https://medium.com/@MAALP/authentication-bypass-using-response-manipulation-6c33eb1257ac

## Broken-link hijacking
1. Broken-link hijacking
    * manual check the external link
    * broken-link-checker
          * blc -rof --filter-level 3 https://example.com/
    * register the broken link or try to get that username or url
    * extra ways:
          * https://ahrefs.com/broken-link-checker
           * https://brokenlinkcheck.com/

## Broken auth and session management
1. Session based bugs
    * Old session does not expire
      * log in the account through two browser(eg. firefox n chrome)
      * change pwd or reset pwd on one browser, then refresh the other browser to see if still logged in
    * Session hijacking (intended behavior)
      * login the acc
      * copy all cookies n logout
      * paste the cookies n refresh
    *  Password reset link token not expire (insecure configurability)
      * create acc, request a forget pwd link
      * don't use it, instead, change the email to new one
      * now use the forget pwd link to sent info to the old email n see if pwd is able to change
    * Server security misconfiguration
      * login, play around n logout, goback one page, see if acc is logged in or can view pages limits to the user

## Bypassing CSP
1. CSP (Content Security Policy) bypass
        * ref: https://kathan19.gitbook.io/howtohunt/bypassing-csp/csp_bypass
