---
title: Source code review
toc: true
date: 2021-04-14 13:50:11
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1. important functions first
      When reading source code, focus on important functions such as authentication, password reset, state-changing actions and sensitive info reads. (What is the most important would depend on the application.) Then, review how these components interact with other functionality. Finally, audit other less sensitive parts of the application.
1. follow user input
      Another approach is to follow the code that processes user input. User input such as HTTP request parameters, HTTP headers, HTTP request paths, database entries, file reads, and file uploads provide the entry points for attackers to exploit the application’s vulnerabilities.This may also help us to find some critical vulnerabilities like xxe,xxs,sql injection
1. hard-coded secrets n credentials
      Hard-coded secrets such as API keys, encryption keys and database passwords can be easily discovered during a source code review. You can grep for keywords such as “key”, “secret”, “password”, “encrypt” or regex search for hex or base64 strings (depending on the key format in use).
1. use of dangerous functions n outdated dependencies
      Unchecked use of dangerous functions and outdated dependencies are a huge source of bugs. Grep for specific functions for the language you are using and search through the dependency versions list to see if they are outdated.
1. developer comments, hidden debug functionalities, configuration files, and the .git directory
      These are things that developers often forget about and they leave the application in a dangerous state. Developer comments can point out obvious programming mistakes, hidden debug functionalities often lead to privilege escalation, config files allow attackers to gather more information about your infrastructure and finally, an exposed .git directory allows attackers to reconstruct your source code.
1. hidden paths, deprecated endpoints, and endpoints in development
      These are endpoints that users might not encounter when using the application normally. But if they work and they are discovered by an attacker, it can lead to vulnerabilities such as authentication bypass and sensitive information leak, depending on the exposed endpoint.
1. weak cryptography or hashing algorithms
      This is an issue that is hard to find during a black-box test, but easy to spot when reviewing source code. Look for issues such as weak encryption keys, breakable encryption algorithms, and weak hashing algorithms. Grep for terms like ECB, MD4, and MD5.
1. missing security checks on user input and regex strength
      Reviewing source code is a great way to find out what kind of security checks are missing. Read through the application’s documentation and test all the edge cases that you can think of. A great resource for what kind of edge cases that you should consider is PayloadsAllTheThings.(github)
1. missing cookie flags
      Look out for missing cookie flags such as httpOnly and secure.
1. unexpected behavior, conditionals, unnecessarily complex and verbose functions
      Additionally, pay special attention to the application’s unexpected behavior, conditionals, and complex functions. These locations are where obscure bugs are often discovered.
1. How to download the index.php source code?
      *  index.php.bak
1. Vim 临时生成的缓存：
      *  第一次意外退出，生成缓存文件 .filename.txt.swp (注意最前面有个.)
      *  第二次生成 .filename.txt.swo， 第三次生成 .filename.txt.swn
      *  需要用vim -r file 打开
1. 各种其它泄漏文件后缀：
      *  .git/ (https://github.com/BugScanTeam/GitHack)
          * git log: 注意查看git历史;ref:https://writeup.ctfhub.com/Skill/Web/%E4%BF%A1%E6%81%AF%E6%B3%84%E9%9C%B2/Git%E6%B3%84%E9%9C%B2/5YAMX5UKxbg5Z2XBCHhxPY.html
          * stash:
              *  查找stash: git stash list; git stash pop;
              *  没有查到stash记录的方法： cat .git/refs/stash 拿到stash的hash; git diff *the hash*
      *  .svn/ (https://github.com/kost/dvcs-ripper.git):
              * wc.db-nodes中记录了服务器文件名
              * 该工具的优势是下载了文件的同时也下载svn的源码结构
      *   Mercurial/HG (https://github.com/kost/dvcs-ripper.git)
              * 
      *  .DS_Store (python-dsstore)
