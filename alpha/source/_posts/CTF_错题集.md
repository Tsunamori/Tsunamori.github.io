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
1. BugKu web18 https://ctf.bugku.com/challenges/detail/id/85.html
    *  考点：爬虫复习、利用eval()直接计算string内的数字公式、利用同一个Session进行get和post请求，防止网页动态刷新计算值失效、post请求中data注意格式为{‘somevtext’：’somevalue’}、python和PHP的计算方式不太一样，可能存在计算偏差导致得不到flag，需要重复发送几次。
    ```
import requests
from bs4 import BeautifulSoup

url = 'http://114.67.246.176:14925/'

s = requests.session()

r = s.get(url)
# 这里要根据网页的编码来设置解码，这个目标网站编码为'utf-8'
r.encoding = 'utf-8'
soup=BeautifulSoup(r.text,'lxml')
data=soup.select('div')
for item in data:
    #获取原文为804134448*284526014-53361872*1933492032+466213037-393751154*4253881+1224286171-1203351395*1548147628+1689398514=?;
    result=item.get_text()

result1=result[0:len(result)-3] #去掉后三位‘=?;’

result2 = eval(result1) #计算数值

result3={'value':result2} #添加post请求数据格式

mssg=s.post(url, data=result3) #利用同一个session，防止网页刷新。

print(mssg.text)
    ```
