---
title: Misconfigurations
toc: true
date: 2021-04-14 14:10:28
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. default credential n admin panel
    * default credentials basics
    default software configurations, ie.:

    ```
https://www.target.com/admin
https://www.target.com/admin-console
https://www.target.com/console
https://admin.target.com
https://admin-console.target.com
https://console.target.com
```

    * get access from third party
        * search the service default credentials if the admin is working on a third party
    * bypass to get access to login page
        * when visiting admin login page is denied, use header injection to bypass, eg, `X-Orginal-URL: /admin` or `X-Rewrite-URL:/admin`
