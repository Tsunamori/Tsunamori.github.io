---
title: 账户取代
toc: true
date: 2021-04-14 10:44:43
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

## Chaining Low Impact Bugs with Xss

1. Have a session hijacking -> Look for xss to stole the cookies n expand the impact

## No Rate Limit On Login with Weak Password Policy

1. The target has weak password policy -> Create a (very) weak password n brute force this account to show the POC

## Password Reset Poisoning Leads To Token Theft

1. Password reset poisoning:
    * Go to password reset function -> Enter email n intercept
    * Change host header to some other host i.e,
          * Host:target.com
           * Host:attacker.com
also try to add some headers without changing host like
          * X-Forwarded-Host: evil.com
           * Referrer: https ://evil.com
    * Forward this if you found that in next request attacker.com means you successfully theft the token.:)

## Using CSRF

1. Using CSRF:
    * Change Password function
    * Email change
    * Change Security Question

## Using Sensitive Data Exposure

1. git recon
1. google recon

## Token Leaks In Response

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
