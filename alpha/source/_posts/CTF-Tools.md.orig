---
title: CTF_Tools
toc: true
date: 2021-04-23 16:48:56
tags: Tools
categories: [100 Cyber security, 120 CTF]
---

### 综合

1. 在线正则表达式  https://regex101.com/
1. CTF在线工具 http://ctf.ssleye.com/
1. js美化 https://beautifier.io/
1. 在线代码运算：https://www.anycodes.cn/#/OnlineCode
1. CTF综合工具：https://www.ctftools.com/down/ （看起来就很全）
1. 文本处理（大小写、去重、替换、排序）：https://www.iamwawa.cn/daxiaoxie.html


<<<<<<< Updated upstream
## 信息泄漏
=======
### 信息泄漏
>>>>>>> Stashed changes

#### 查看常用CTF文件是否存在于网站中
https://github.com/WangYihang/SourceLeakHacker （扫目录不如dirsearch但更偏向于扫专门的flag文件啥的）

#### 常规git泄漏
https://github.com/BugScanTeam/GitHack

<<<<<<< HEAD
## 漏洞利用

#### SSTI/flask模板注入
https://github.com/epinna/tplmap （python2环境，配环境有点麻烦，但装好就相当好使）

=======
<<<<<<< Updated upstream
>>>>>>> 48e6b287eb9e8384b6db52752c1b3e52aa6feb7c
## 密码加解密
=======
### 密码加解密
>>>>>>> Stashed changes

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
http://tool.chinaz.com/tools/imgtobase/ （密文开头：data:image/jpg;base64,/9j/）

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
公私钥：https://www.jianshu.com/p/f3a364a880d7
RSA题集：https://github.com/yifeng-lee/RSA-In-CTF
当n,e,uncipher特别大时可以使用rsactftools：https://github.com/Ganapati/RsaCtfTool
当存在n,e,enc时的最新使用方法：https://github.com/Ganapati/RsaCtfTool/issues/247
    ```
eg:
python3 RsaCtfTool.py -n 73858245204393269120598772974257218874289179816258533748548954836353808000542909731398449660121424717248079724923923284922221800385870164798744402737063530114487159042350959711492581286948377310386675916734743955708759421607349585475823504183537132290212235481788134354234151518170214048887523664730897405647 -e 40185337488228965483088768689361237580655397969352079774015797079626751211817603607155962600219370112069853693506378621129705581863959239984871073674637310005220323695488701830477574210150173980203084059519439551150923764323395592278383658383525721471839777239932693638775164411928659371101425091253226312809 --uncipher 11279019002658132246395680583012680197641520463055015905954159065596263829466315684813964601637222796742820040947867106250604831640789683230174556440623073026811900236014477245382133062014821631152893829897692611106964558955134460480794092202226246351625569961925929573208935964302390747204250124554416009841 --attack wiener
    ```

#### 银河字母（Standard Galactic Alphabet）
https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%93%B6%E6%B2%B3%E5%AD%97%E6%AF%8D

#### base64
Base64是一种基于64个可打印字符来表示二进制数据的表示方法。由于 2的6次方为64 ，所以每6个位元为一个单元，对应某个可打印字符。3个字节相當於24个位元，对应于4个Base64单元，即3个字节可由4个可打印字符来表示。在Base64中的可打印字符包括字母A-Z、a-z、数字0-9，这样共有62个字符，此外两个可打印符号在不同的系统中而不同。

#### 元音密码
http://www.tuilixy.net/forum.php?mod=viewthread&tid=3538&mobile=no
FLAG=21.33.1.22
```
这是一种利用音标中的元音进行加密的密码，编写简便，非常实用。它是根据事先编制好的密码表来对信息加密的。
密码表的编写办法是：先写下26个字母，在每个元音字母下标上数字。如A=1，E=2，I=3，O=4，U=5.然后根据每个元音右边的辅音次序，将每个辅音下标上相应的数字。如B位于元音A右边第一的位置，所以它用数字11代替（意思是第一个元音右边第一个字母）；Y位于元音U右边第四的位置，因而代替它的数字是54（详见密码表）
A    B    C    D    E    F   G    H    I    J    K   L    M    N
1    11   12   13   2    21  22   23   3    31   32  33   34   35
O    P    Q    R    S    T   U    V    W   X    Y   Z
4    41   42   43   44   45  5    51   52  53   54  55

下面就可以对信息进行加密了。比如明文是：When in Rome,do as the Romans do.（入乡随俗）根据密码表加密为（注意，数字之间加点）：
52.23.2.35.3.35.43.4.34.2.13.4.1.44.45.2.43.4.34.1.35.44.13.4
经过加密，原来的英文字母变成了一串莫名其妙的数字。不过，这种密码很容易被识别是何种密码。只要仔细观察，就可发现它的“特征”。如最大的数字不超过55；里面反复出现1，2，3，4，5这几个单数，从没有18，27，39等数字等。为了使破译难度更困难，我们可以略微加大它的保密程度。
方法是：将它们变成一对对的数字，然后再用栅栏法等进行易位加密。五个元音字母的单数可变成10，20，30，40，50等双数。
```
#### 隐藏字符加密
http://www.atoolbox.net/Tool.php?Id=829
原理是利用零宽字符对加密文本进行转码，嵌入到普通文本当中，从而隐藏加密内容；表面看起来是一段普通文本，复制粘贴不会丢失。零宽字符在大部分应用都支持，pc版QQ会显示零宽字符，但移动端不显示。

#### 社会主义核心价值观加密
http://www.atoolbox.net/Tool.php?Id=850
顾名思义。由公平公正blabla组成。

#### 音符加密
https://www.qqxiuzi.cn/bianma/wenbenjiami.php?s=yinyue
