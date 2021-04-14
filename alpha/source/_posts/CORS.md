---
title: CORS
toc: true
date: 2021-04-14 11:49:41
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

## CORS
***ref: https://www.packetlabs.net/cross-origin-resource-sharing-cors/***
1. misconfigured CORS
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

## CORS bypass
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
