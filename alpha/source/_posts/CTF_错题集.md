---
title: 错题wp集
toc: true
date: 2021-04-22 14:57:37
tags: CTF
categories: [100 Cyber security, 120 CTF, 121 Web ]
---


1. SUCTF 2019 EasySQL https://blog.csdn.net/qq_43619533/article/details/103434935
    *  考点： 通过输入非零数字得到的回显1和输入其余字符得不到回显来判断出内部的查询语句可能存在有||
1. BugKu web 21 https://blog.csdn.net/qq_41333578/article/details/92759619
    *  考点： 弱比较的绕过; PHP伪协议php://input如何输入请求主体; eregi绕过的两种方式; 注意`\x00`会截断后续内容，而%00不会。
1. BugKu 冬至红包 https://blog.csdn.net/qq_46230755/article/details/112973722
    *  考点： 新姿势
