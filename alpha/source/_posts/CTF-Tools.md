---
title: CTF_Tools
toc: true
date: 2021-04-23 16:48:56
tags: Tools
categories: [100 Cyber security, 120 CTF]
---

1. 在线正则表达式  https://regex101.com/
1. CTF在线工具 http://ctf.ssleye.com/
1. js美化 https://beautifier.io/
1. 在线代码运算：https://www.anycodes.cn/#/OnlineCode
1. CTF综合工具：https://www.ctftools.com/down/ （看起来就很全）


### 信息泄漏
#### 查看常用CTF文件是否存在于网站中
https://github.com/WangYihang/SourceLeakHacker （扫目录不如dirsearch但更偏向于扫专门的flag文件啥的）
#### 常规git泄漏
https://github.com/BugScanTeam/GitHack

### 密码加解密
#### 摩斯密码
    http://ctf.ssleye.com/morse.html （有特殊符号的时候好用一点）
    https://www.ctftools.com/down/down/passwd/ （可以自己设置分割符和格式，比较自由）
#### 加解密工具合集
    https://ctf.bugku.com/tools （大概算常用类型？）
    http://www.atoolbox.net/Category.php? （充数的）
    http://ctf.ssleye.com （上面提到过的）
    https://www.boxentriq.com/code-breaking （英文，好使）
    https://www.dcode.fr/en （英文，相对上面那个来说冷门的加密更多一些）
#### base64与图片互转
http://tool.chinaz.com/tools/imgtobase/（密文开头：data:image/jpg;base64,/9j/）
#### 博多密码
https://www.boxentriq.com/code-breaking/baudot-code （切换格式自动解码）
#### 将字母转换为数字（如hill希尔加密）
https://www.boxentriq.com/code-breaking/letters-to-numbers
#### 黄道十二宫杀手解密：
    工具：https://m.majorgeeks.com/files/details/azdecrypt.html
    wp：https://blog.csdn.net/qq_43625917/article/details/113623475
#### 编码格式转换
converter https://blog.csdn.net/pdsu161530247/article/details/74640746
    CTFCrackTools.git
#### 累次加密
10进制的ASCII码依次增加。ref:https://blog.csdn.net/pdsu161530247/article/details/78149818
#### 仿射密码（Affine cipher）
https://www.dcode.fr/affine-cipher
#### 各种编码的识别
https://zh.codeprj.com/blog/983e0f1.html
#### utf-7
 格式为`+/v+ +Ablabla-`，只取+Ablabla-这部分进行翻译。
#### Windows hash
 ref：https://zh.codeprj.com/blog/983e0f1.html
    Windows系統下的hash密碼格式為：`用戶名稱:RID:LM-HASH值:NT-HASH值`
    Tips：LMHASH，NTHASH，Ophcrack可知這是windows的hash密碼
    工具：John the Ripper
    如：
    ```
nick:1003:8EA9109FDA91D6BFC6EBE8776A153FEB:C6002D00F57F9F399B6263D714AF8C3A:::
8EA9109FDA91D6BFC6EBE8776A153FEB:C6002D00F57F9F399B6263D714AF8C3A解碼后就是密碼
用戶名稱為：nick
RID為：1003
LM-HASH值為：8EA9109FDA91D6BFC6EBE8776A153FEB
NT-HASH值為：C6002D00F57F9F399B6263D714AF8C3A
在https://www.objectif-securite.ch/en/ophcrack 解密的passwd為hacker521，登陸就能得到flag
root:$6$HRMJoyGA$26FIgg6CU0bGUOfqFB0Qo9AE2LRZxG8N3H.3BK8t49wGlYbkFbxVFtGOZqVIq3qQ6k0oetDbn2aVzdhuVQ6US.:17770:0:99999:7:::
用戶名root
RID 6
LM-HASH值為26FIgg6CU0bGUOfqFB0Qo9AE2LRZxG8N3H
NT-HASH  3BK8t49wGlYbkFbxVFtGOZqVIq3qQ6k0oetDbn2aVzdhuVQ6US
    ```
#### RSA：
    知识背景：https://zhuanlan.zhihu.com/p/76017554
    当n,e,uncipher特别大时可以使用rsactftools：https://github.com/Ganapati/RsaCtfTool
    当存在n,e,enc时的最新使用方法：https://github.com/Ganapati/RsaCtfTool/issues/247
    ```
eg:
python3 RsaCtfTool.py -n 73858245204393269120598772974257218874289179816258533748548954836353808000542909731398449660121424717248079724923923284922221800385870164798744402737063530114487159042350959711492581286948377310386675916734743955708759421607349585475823504183537132290212235481788134354234151518170214048887523664730897405647 -e 40185337488228965483088768689361237580655397969352079774015797079626751211817603607155962600219370112069853693506378621129705581863959239984871073674637310005220323695488701830477574210150173980203084059519439551150923764323395592278383658383525721471839777239932693638775164411928659371101425091253226312809 --uncipher 11279019002658132246395680583012680197641520463055015905954159065596263829466315684813964601637222796742820040947867106250604831640789683230174556440623073026811900236014477245382133062014821631152893829897692611106964558955134460480794092202226246351625569961925929573208935964302390747204250124554416009841 --attack wiener
    ```
#### 银河字母（Standard Galactic Alphabet）
https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%93%B6%E6%B2%B3%E5%AD%97%E6%AF%8D
