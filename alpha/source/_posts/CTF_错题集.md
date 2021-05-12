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
1. BugKu Web 32
题目：文件上传;My name is margin,give me a image file not a php

* 解题思路：文件上传漏洞，掏出上传fuzz试试看。
  fuzz没过，看评论说是把multipart/form-data;修改成Content-Type: mulTipart/form-data; ，然后修改php文件为php4，竟然还有这种操作，学到了。

1. BugKu Web 33
提示：
```
Flag:{xxx}
fR4aHWwuFCYYVydFRxMqHhhCKBseH1dbFygrRxIWJ1UYFhotFjA=
 // }l.&W'EG*B(W[(+G'U-0
```
题目：
```
<?php
function encrypt($data,$key)
{
    $key = md5('ISCC');
    $x = 0;
    $len = strlen($data);
    $klen = strlen($key);
    for ($i=0; $i < $len; $i++) {
        if ($x == $klen)
        {
            $x = 0;
        }
        $char .= $key[$x];
        $x+=1;
    }
    for ($i=0; $i < $len; $i++) {
        $str .= chr((ord($data[$i]) + ord($char[$i])) % 128);
    }
    return base64_encode($str);
}
?>
```

* 解题思路：在线解base64的时候还没注意到，复制下来才发现还有特殊字符。`.=`连接赋值运算符，将右边参数附加到左边的参数之后。完整的看了一遍代码，发现应该是得到提示的base64字符串的算法。懒得配环境，对PHP一直是只会读代码不会写应用的状态，干脆在网上找了找PHP在线运行网站。试着运行了一下原算法用不同的数字和字母输入data内容。
根据提示中的base64推断，data长度为20。
逆推写了一下获取data的源码：
```
<?php
$str='fR4aHWwuFCYYVydFRxMqHhhCKBseH1dbFygrRxIWJ1UYFhotFjA=';
$str_decode = base64_decode($str);
$len1=strlen($str_decode);

$key = md5('ISCC');  # 729623334f0aa2784a1599fd374c120d
$klen = strlen($key);
$char = '';
$data = '';
for ($i=0; $i < $len1; $i++) {
        if ($x == $klen)
        {
            $x = 0;
        }
        $char .= $key[$x]; # 729623334f0aa2784a1599fd374c120d729623
        $x+=1;
    }
# print($len1); -> 38
# print($char); -> 729623334f0aa2784a1599fd374c120d729623
for ($i=0; $i < $len1; $i++) {
        $data .= chr((ord($str_decode[$i])+128)-ord($char[$i]));
    }
print($data);    
?>
```
得到结果：lag{asdqwdfasfdawfefqwdqwdadwqadawd}，提示里写了Flag:{xxx}，所以把结果改成Flag:{asdqwdfasfdawfefqwdqwdadwqadawd}，结果正确。
