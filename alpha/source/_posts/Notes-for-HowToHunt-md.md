---
title: Notes for HowToHunt.md
toc: true
date: 2021-04-08 16:05:57
categories: [100 Cyber security, 110 Bug bounty, 119 Notes]
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
  ***ref: https://infosecwriteups.com/an-unexpected-bounty-email-bounce-issues-b9f24a35eb68***
1. Long password DoS:
    * Test in Forgot Password Page n Change Password, cuz the registration usually have length limit but others don't.
    * Use a password around 150-200 words to check if there has length limit. Then choose a longer password n keep checking the response time, to see if the app crashes for few seconds.
    * reason: The long password will cause a problem while hashing it for storing in dataset.
1. Long string DoS:
    * Create app and put field like username or address or even profile picture name parameter (second reference) like 1000 character of string .
    * Search A's account from B's account either it will either keeping searching for long time or crash(500 Error code).
      ***ref: https://shahjerry33.medium.com/long-string-dos-6ba8ceab3aa0***
      ***https://hackerone.com/reports/764434***

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
      ***ref: https://medium.com/@surendirans7777/2fa-bypass-techniques-32ec135fb7fe***
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
      ***ref: https://medium.com/@AGNIHACKERS/otp-bypass-through-response-manipulation-beeb467359d8***
1. OTP bypass via response manipulation
    * change any status, false, error, 0, to true, success, 1. And see if it can bypass the OTP
      ***ref: https://medium.com/@MAALP/authentication-bypass-using-response-manipulation-6c33eb1257ac***

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
  ***ref: https://kathan19.gitbook.io/howtohunt/bypassing-csp/csp_bypass***

## CMS
1. Wordpress
    * Wordpress Detection
      * Wappalyzer
      * WhatRuns
      * BuildWith
    * General Scan Tool
      * WpScan
    * xmlrpc.php
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

    * CVE-2018-6389 (wp <= 4.9.3)
      * detection
        * https://gist.github.com/remonsec/4877e9ee2b045aae96be7e2653c41df9
      * exploit
        * use DDoS (Doser https://github.com/quitten/doser.py)

        ```
python3 doser.py -t 999 -g 'https://site.com/fullUrlFromLoadsxploit'
        ```

    * WP Cornjob DOS
      * detection
        * view site.com/wp-cron.php, You will see a Blank page with 200 HTTP status code
      * exploit
        * same tool as above (Doser)

        ```
        You will see a Blank page with 200 HTTP status code
        ```
        ***ref: https://medium.com/@thecpanelguy/the-nightmare-that-is-wpcron-php-ae31c1d3ae30***
    * WP User Enumeration
      * site.com/wp-json/wp/v2/users/
      * mostly not acceptable, can be used to extend the impact tho

## CORS
1. CORS (***ref: https://www.packetlabs.net/cross-origin-resource-sharing-cors/***)
  * misconfigured CORS
    * hunting method 1 (single target)
      * crawl the target website n search Access-Control using burp
      * Try to add Origin Header i.e, Origin:attacker.com or Origin:null or Origin:attacker.target.com or Origin:target.attacker.com
      * If origin is reflected in response means the target is vuln to CORS
    * hunting method 2 (multiple means including subdomains)
      * find alive subdomains (i.e, subfinder -d domain.com -o target.txt; grep alive: cat target.txt | httpx | tee -a alive.txt), send each subdomain into burp
      * same as above method
    * automated tools
      * https://github.com/chenjj/CORScanner
      * https://github.com/lc/theftfuzzer
      * https://github.com/s0md3v/Corsy
      * https://github.com/Shivangx01b/CorsMe
2. CORS bypass
  * Origin:null
  * Origin:attacker.com
  * Origin:attacker.target.com
  * Origin:attackertarget.com
  * Origin:sub.attackertarget.com
  * Origin:attacker.com and then change the method Get to post/Post to Get
  * Origin:sub.attacker target.com
  * Origin:sub.attacker%target.com
  * Origin:attacker.com/target.com
  * https://twitter.com/trbughunters/status/1287023673845612546
  * https://twitter.com/Paresh_parmar1/status/1265251507655630848
  * https://www.corben.io/tricky-CORS/
  * https://medium.com/@virus0x01/cors-misconfiguration-leading-to-private-information-disclosure-3034cfcb4b93

## CSRF
1. CSRF
    * https://kathan19.gitbook.io/howtohunt/csrf/csrf
1. CSRF bypass
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

## Finding CVEs
1. CVEs
      1.Grab all the subdomains i.e, subfinder -d domain.com | tee -a domains.txt
      2.Grap all alive domains i.e,  cat domains.txt | httpx -status-code | grep 200 | cut -d " " -f1 | tee -a alive.txt
      3.Run nuclei basic-detection,panels,workflows,cves templates differently and store results in different file. i.e, cat alive.txt | nuclei -t nuclei-templates/workflows | tee -a workflows.
      4.Read each output carefully with patience.
      5.Find interest tech used by target. i.e, jira
      6.put that link into browser check the version used by target.
      7.Go on google search with jira version exploit.
      8.grep the cves
      9.Go to twitter in explore tab search CVE(that you found from google) poc or CVE exploit
      10.Go to google and put cve or some details grab from   twitter for a better poc read writeups related to that.
      11.Try all cves if success report it.:)

## Check list
1. Web application pentesting checklist
https://kathan19.gitbook.io/howtohunt/checklist/web-application-pentesting-checklist
1. Web checklist 2
https://github.com/KathanP19/HowToHunt/blob/master/CheckList/Web_Checklist_by_Chintan_Gurjar.pdf
1. Web checklist 3
https://github.com/KathanP19/HowToHunt/blob/master/CheckList/mindmap.png

## Source code review
1. code review tips
    * important functions first
      When reading source code, focus on important functions such as authentication, password reset, state-changing actions and sensitive info reads. (What is the most important would depend on the application.) Then, review how these components interact with other functionality. Finally, audit other less sensitive parts of the application.
    * follow user input
      Another approach is to follow the code that processes user input. User input such as HTTP request parameters, HTTP headers, HTTP request paths, database entries, file reads, and file uploads provide the entry points for attackers to exploit the application’s vulnerabilities.This may also help us to find some critical vulnerabilities like xxe,xxs,sql injection
    * hard-coded secrets n credentials
      Hard-coded secrets such as API keys, encryption keys and database passwords can be easily discovered during a source code review. You can grep for keywords such as “key”, “secret”, “password”, “encrypt” or regex search for hex or base64 strings (depending on the key format in use).
    * use of dangerous functions n outdated dependencies
      Unchecked use of dangerous functions and outdated dependencies are a huge source of bugs. Grep for specific functions for the language you are using and search through the dependency versions list to see if they are outdated.
    * developer comments, hidden debug functionalities, configuration files, and the .git directory
      These are things that developers often forget about and they leave the application in a dangerous state. Developer comments can point out obvious programming mistakes, hidden debug functionalities often lead to privilege escalation, config files allow attackers to gather more information about your infrastructure and finally, an exposed .git directory allows attackers to reconstruct your source code.
    * hidden paths, deprecated endpoints, and endpoints in development
      These are endpoints that users might not encounter when using the application normally. But if they work and they are discovered by an attacker, it can lead to vulnerabilities such as authentication bypass and sensitive information leak, depending on the exposed endpoint.
    * weak cryptography or hashing algorithms
      This is an issue that is hard to find during a black-box test, but easy to spot when reviewing source code. Look for issues such as weak encryption keys, breakable encryption algorithms, and weak hashing algorithms. Grep for terms like ECB, MD4, and MD5.
    * missing security checks on user input and regex strength
      Reviewing source code is a great way to find out what kind of security checks are missing. Read through the application’s documentation and test all the edge cases that you can think of. A great resource for what kind of edge cases that you should consider is PayloadsAllTheThings.(github)
    * missing cookie flags
      Look out for missing cookie flags such as httpOnly and secure.
    * unexpected behavior, conditionals, unnecessarily complex and verbose functions
      Additionally, pay special attention to the application’s unexpected behavior, conditionals, and complex functions. These locations are where obscure bugs are often discovered.

## EXIF geo data not stripped
1. EXIF geo data not stripped (sensitive info leaked)
    * tools
      http://exif.regex.info/exif.cgi

## File upload bypass
1. File upload bypass
    * bypassing normal extension
        sth.php.jpg or sth.jpg.php
    * bypassing the magic byte validation
        polyglots

## Find origin IP
1. find origin
    * https://kathan19.gitbook.io/howtohunt/find-origin-ip/findorigin

## HTTP desync attack
1. HTTP_Desync
