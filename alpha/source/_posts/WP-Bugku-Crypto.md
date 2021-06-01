---
title: WP-Bugku-Crypto
toc: true
date: 2021-05-17 17:02:34
tags: [CTF, WriteUp]
categories: [100 Cyber security, 120 CTF, 124 Crypto ]
---

#### /.-
提示： ..-./.-../.-/--./----.--/-../...--/..-./-.-./-.../..-./.----/--.../..-./----./...--/----./----./...../-----/....-/-----.-
* 解题思路：搞了半天，因为有前后两个ASCII码所以直接写pycode总是很难搞，还是从网上找了在线工具：https://www.ctftools.com/down/down/passwd/，相当方便了。

#### 聪明的小羊
提示：一只小羊翻过了2个栅栏 fa{fe13f590lg6d46d0d0}
* 解题思路：根据提示可得栅栏加密。

#### ok
提示：Ook.
* 解题思路：根据提示，可得brainfuck/Ook加密。

#### [+-<>]
提示：`+++++ +++++ [->++ +++++ +++<] >++.+ +++++ .<+++ [->-- -<]>- -.+++ +++.< ++++[ ->+++ +<]>+ +++.< +++++ +++[- >---- ----< ]>--- ----- ---.< +++++ ++[-> +++++ ++<]> +++.< +++++ +[->- ----- <]>-- ----- -.--. ----. --.++ +++++ +.<++ ++++[ ->+++ +++<] >++++ +.++. <++++ ++[-> ----- -<]>- ----- ----. -.<++ +++++ [->++ +++++ <]>+. ----. ++++. <++++ +++[- >---- ---<] >---- .+.<+ +++++ ++[-> +++++ +++<] >++++ +++++ ++.<`
* 解题思路：跟上个一样，这个是brainfuck。

#### 把猪困在猪圈里
提示： flag{}
* 密文开头有个`/9j/`，结尾有等号，是比较明显的base64转图片的提示。利用http://tool.chinaz.com/tools/imgtobase/网站解密，在开头加`data:image/jpg;base64,`补全图片头，得到猪圈密码图片，对照密码表解密得到flag。

#### 你喜欢下棋吗
提示：解压密码为小写
4423244324433534315412244543
* 解题思路：下棋，提示了这是棋盘密码。查了一下，棋盘密码有两种，一种是Polybius密码，密文为两两组合的数字，另一种是ADFGX密码，密文为两两组合的字母。
但是看在线的解密都要密钥，看别人的WP才发现他们解密用的常见密码表，行吧。
这里得到密码，解压文件，得到下一个密文
```
一种5bit的编码
bugku里面的内容为小写
bugku{11111 11001 00011 00111 01001 11011 10110 11111 10000 01110 11011 10110 11111 01001 00001}
```
看评论是博多密码（我只是个密码小白我啥也不知道），直接解得到flag。

#### 小山丘的秘密
提示： hill能有什么秘密呢
* 解题思路：既然提到了hill，那么就迅速查一下有没有叫hill的加密。果然有，中文叫做希尔密码。
下载包里很明显是密钥，也就是
```
{1,2,3}
{0,1,4}
{5,6,0}
```
再看flag文件，A为1，PLGTGBQHM翻译一下就是16 12 7 20 7 2 17 8 13 。（这里注意一下，密钥也跟随A=1，一开始还不确定，用工具解密的时候发现的）
（这里居然找到了在线的工具，一个是字母转换数字，一个是希尔加密`https://www.boxentriq.com/code-breaking/letters-to-numbers`，`https://www.dcode.fr/hill-cipher`）

#### EN-气泡
题目：
```
xivak-notuk-cupad-tarek-zesuk-zupid-taryk-zesak-cined-tetuk-nasuk-zoryd-tirak-zysek-zaryd-tyrik-nisyk-nenad-tituk-nysil-hepyd-tovak-zutik-cepyd-toral-husol-henud-titak-hesak-nyrud-tarik-netak-zapad-tupek-hysek-zuned-tytyk-zisuk-hyped-tymik-hysel-hepad-tomak-zysil-nunad-tytak-nirik-copud-tevok-zasyk-nypud-tyruk-niryk-henyd-tityk-zyral-nyred-taryk-zesek-corid-tipek-zysek-nunad-tytal-hitul-hepod-tovik-zurek-hupyd-tavil-hesuk-zined-tetuk-zatel-hopod-tevul-haruk-cupod-tavuk-zesol-ninid-tetok-nasyl-hopid-teryl-nusol-heped-tovuk-hasil-nenod-titek-zyryl-hiped-tivyk-cosok-zorud-tirel-hyrel-hinid-tetok-hirek-zyped-tyrel-hitul-nyrad-tarak-hotok-cuvux
```
* 解题思路：en气泡，气泡的英文就是bubble嘛，搜索bubble crypto，看结果不太行，改关键词为bubble cipher。还是没有，直接搜bubble加密，才看到了其他人对别的题的wp，太坑了。以及这个加密名字叫`BubbleBabble`。
于是借鉴了一下wp借鉴的代码（。。。）
```
from bubblepy import BubbleBabble

str='blabla'
Str=BubbleBabble()
print(Str.decode(str))
```

#### 你以为是md5吗
提示：md5的构成
题目：bci177a7a9c7udf69c248647b4dfc6fd84o
* 解题思路：既然提示都这么说了，那就查一查md5的构成。
然后发现密文不太对，是35位，再根据评论的提示，md5是32位的16进制数，所以去掉超过16进制范围的部分，丢进md5解密。

#### easy_crypto
提示：01
* 解题思路：摩斯密码。

#### 黄道十二官
* 解题思路：点开图一脸蒙蔽，查了查才发现是个古老的案子中的加密，还挺有意思的。
然并卵，还是借鉴了别人另一道题的wp以及代码（顺便说dcode.fr的那个解密不太行），ref：https://blog.csdn.net/qq_43625917/article/details/113623475
自己改了代码：
```
s1=r'%,,@*>@?==%88%5'*9
s2=r',@%#@@90-7$^=*@'*9
s3=r'17,(>()1@##-$40'*9
s4=r'~,*6?#%#8#=75+1'*9
s5=r'(*@*1%#>,0@5%?'*9
s6=r'%*^=)&>=1%,+7&#'*9
s7=r'8681(+8*@@(,@@@'*9
s8=r'#*=#$3*#%,#%%,3'*9
s9=r',*+7,7+@===+)61'*9

tmp=''
for i in range(15):
    tmp += s1[i]+s2[i+2]+s3[i+4]+s4[i+6]+s5[i+8]+s6[i+10]+s7[i+12]+s8[i+14]+s9[-1]

def cut(obj, sec):
    str_list = [obj[i:i+sec] for i in range(0,len(obj),sec)]
    print(str_list)
    return str_list

l1=cut(tmp,15)
for i in l1:
    print (i)
```
然后用专用的工具AZdecrypt（https://m.majorgeeks.com/files/details/azdecrypt.html）解密。（代码写的还是有问题，这个先放一放）

#### 简单加密
* 提示：e6Z9i~]8R~U~QHE{RnY{QXg~QnQ{^XVlRXlp^XI5Q6Q6SKY8jUAA
解题思路：加密新手毫无灵感，按照AA转为==的思路转换密文，再base64解密。
```
text='e6Z9i~]8R~U~QHE{RnY{QXg~QnQ{^XVlRXlp^XI5Q6Q6SKY8jUAA'
text1=[(ord(c)-4) for c in text]
text2=''.join(chr(c) for c in text1)

print (text2)
```

#### 散乱的密文
* 题目： lf5{ag024c483549d7fd@@1}  一张纸条上凌乱的写着2 1 6 5 3 4
解题思路：根据开头的flag大概知道后面的数字是提示了顺序，再看密文长度为24,推断是每六位按照数字顺序打乱。
这密文还有问题，顺序不完全对。
解题思路：
```
text='lf5{ag 024c48 3549d7 fd@@1}' #2 1 6 5 3 4
text=text.split(' ')

def calcu(text1):
    text1=list(text1)
    order='2 1 5 6 4 3'
    order= order.split(' ')
    for i in range(0, len(order)):
        order[i] = int(order[i])-1
    list1 = [text1[i] for i in order]
    print(''.join(list1))

for i in text:
    calcu(i)
```

#### .!?
解题思路：看题目，应该是Ook。

#### 一段Base64
解题思路：converter好使。https://blog.csdn.net/pdsu161530247/article/details/74640746

#### 奇怪的密码
* 提示：突然天上一道雷电 gndk€rlqhmtkwwp}z
解题思路：累次加密。（不知道是累次的就照着flag格式硬猜是ASCII增加）
抄个脚本：
```
# -*- coding:utf-8 -*-
c = "gndk{rlqhmtkwwp}z"
i = 0
flag = ""
while i < len(c):
    num = ord(c[i]) - (i + 1)
    flag += chr(num)
    i += 1

print("解密：", flag)
```

#### 托马斯.杰斐逊
* 题目：
```
1： <ZWAXJGDLUBVIQHKYPNTCRMOSFE <
2： <KPBELNACZDTRXMJQOYHGVSFUWI <
3： <BDMAIZVRNSJUWFHTEQGYXPLOCK <
4： <RPLNDVHGFCUKTEBSXQYIZMJWAO <
5： <IHFRLABEUOTSGJVDKCPMNZQWXY <
6： <AMKGHIWPNYCJBFZDRUSLOQXVET <
7： <GWTHSPYBXIZULVKMRAFDCEONJQ <
8： <NOZUTWDCVRJLXKISEFAPMYGHBQ <
9： <QWATDSRFHENYVUBMCOIKZGJXPL <
10： <WABMCXPLTDSRJQZGOIKFHENYVU <
11： <XPLTDAOIKFZGHENYSRUBMCQWVJ <
12： <TDSWAYXPLVUBOIKZGJRFHENMCQ <
13： <BMCSRFHLTDENQWAOXPYVUIKZGJ <
14： <XPHKZGJTDSENYVUBMLAOIRFCQW <

密钥： 2,5,1,3,6,4,9,7,8,14,10,13,11,12

密文：HCBTSXWCRQGLES
```
解题思路：题目翻译成英文大概是thomas jefferson，用关键词thomas jefferson cipher去google，得到wheel cipher，中文应该是杰弗逊圆盘/轮转机加密。但是根据原加密算法看了一下，这个和默认的加密有区别，是根据密钥更改每一行的顺序。
抄个代码，自己改了改：ref:https://www.cnblogs.com/0yst3r-2046/p/11810574.html
```
#! /usr/bin/env python3
# -*- coding: UTF-8 -*-

import numpy as np
#秘钥
key="2,5,1,3,6,4,9,7,8,14,10,13,11,12"
#密文
cipher_text = "HCBTSXWCRQGLES"

f = open("file.txt")
str_first_encry = []

for line in f:
    line = line.strip()
    str_first_encry.append(line)

key_index = key.split(",")
str_second_encry=[]
for k in key_index:
    str_second_encry.append(str_first_encry[int(k)-1])
    print(str_first_encry[int(k)-1])

for i,ch in enumerate(cipher_text):
    line = str_second_encry[i]
    split_index = line.index(ch)
    temp=[]
    temp[0:len(line)-split_index+1] = line[split_index:len(line)]
    temp[len(temp):] = line[0:split_index]
    str_second_encry[i] = "".join(temp)
print("-------------------------------------")

list_store = []
i = 0
for plain in str_second_encry:
    print(plain)
    plain = list(plain)
    list_store.append(plain)
    i += 1

list_store = np.array(list_store)
print("提取每一列内容：")
#翻转读取每一列的内容，使结果更直观
print(np.transpose(list_store))
```

#### 这不是MD5
* 题目： 666c61677b616537333538376261353662616566357d
解题思路：十六进制转字符串。

#### 告诉你个秘密
* 题目：636A56355279427363446C4A49454A7154534230526D6843 56445A31614342354E326C4B4946467A5769426961453067
解题思路：十六进制字符串、base64解码都解出来了，然而万万没想到最后一步是看键盘（物理层面）。

#### 贝斯家族
* 题目：@iH<,{bdR2H;i6*Tm,Wx2izpx2!
解题思路：base91
