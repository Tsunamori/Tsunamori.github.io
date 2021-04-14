---
title: Rate limit
toc: true
date: 2021-04-14 14:15:19
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---


1. rate-limit bypass
    * customizing HTTP methods
        * change the GET request to POST, PUT, etc.
        * bypassing rate-limit in API's try HEAD method
        * using header to bypass the rate limit
          (use the following just below the host header)

          ```
X-Forwarded-For: IP
X-Forwarded-IP: IP
X-Client-IP: IP
X-Remote-IP: IP
X-Originating-IP: IP
X-Host: IP
X-Client: IP

#or use double X-Forwarded-For header
X-Forwarded-For:
X-Forwarded-For: IP
```

    * addding headers to spoof IP
        * add HTTP headers to spoof IP n evade detection

        ```
X-Forwarded: 127.0.0.1
X-Forwarded-By: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Forwarded-For-Original: 127.0.0.1
X-Forwarder-For: 127.0.0.1
X-Forward-For: 127.0.0.1
Forwarded-For: 127.0.0.1
Forwarded-For-Ip: 127.0.0.1
X-Custom-IP-Authorization: 127.0.0.1
X-Originating-IP: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
```

        * bypass rate limit using special characters
          * adding null byte (%00) at the end of the email
          * try add a space after the email (not encoded)
          * other characters can be tried to bypass rate limit `%0d , %2e , %09 , %20 , %0, %00, %0d%0a, %0a, %0C`
          * add a slash (/) at the end of api endpoint
            from`domain.com/v1/login` to `domain.com/v1/login/`
          eg: https://hackerone.com/reports/1067533
        * using IP rotate burp extension
        other ref: https://kathan19.gitbook.io/howtohunt/rate-limit/ratelimitbypass
