---
title: WP-Bugku-Crypto
toc: true
date: 2021-05-17 17:02:34
tags: [CTF, WriteUp]
categories: [100 Cyber security, 120 CTF, 124 Crypto ]
---

#### /.-
提示： ..-./.-../.-/--./----.--/-../...--/..-./-.-./-.../..-./.----/--.../..-./----./...--/----./----./...../-----/....-/-----.-
* 解题思路：搞了半天，因为有前后两个ASCII码所以直接写pycode总是很难搞，还是从网上找了在线工具： https://www.ctftools.com/down/down/passwd/ ，相当方便了。

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
* 密文开头有个`/9j/`，结尾有等号，是比较明显的base64转图片的提示。利用 http://tool.chinaz.com/tools/imgtobase/  网站解密，在开头加`data:image/jpg;base64,`补全图片头，得到猪圈密码图片，对照密码表解密得到flag。

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
然后用专用的工具AZdecrypt（https://m.majorgeeks.com/files/details/azdecrypt.html ）解密。（代码写的还是有问题，这个脚本先看别人的吧）

#### 简单加密
* 提示：
```
e6Z9i~]8R~U~QHE{RnY{QXg~QnQ{^XVlRXlp^XI5Q6Q6SKY8jUAA
```
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

#### python(N1CTF)
解题思路：ref:https://blog.csdn.net/crisprx/article/details/107178198
自己写了好久，还是没写对，看了别人的WP，重点还是在对于Feistel加密结构的了解吧。虽然思路是对了，想办法利用原有代码减少重构量，但是还是没抓准关键点。

#### 进制转换
* 题目： 二进制、八进制、十进制、十六进制，你能分的清吗？
解题思路：没找到合适的脚本，自己写吧。
```
text = 'd87 x65 x6c x63 o157 d109 o145 b100000 d116 b1101111 o40 x6b b1100101 b1101100 o141 d105 x62 d101 b1101001 ' \
       'd46 o40 d71 x69 d118 x65 x20 b1111001 o157 b1110101 d32 o141 d32 d102 o154 x61 x67 b100000 o141 d115 b100000 ' \
       'b1100001 d32 x67 o151 x66 d116 b101110 b100000 d32 d102 d108 d97 o147 d123 x31 b1100101 b110100 d98 d102 ' \
       'b111000 d49 b1100001 d54 b110011 x39 o64 o144 o145 d53 x61 b1100010 b1100011 o60 d48 o65 b1100001 x63 b110110 ' \
       'd101 o63 b111001 d97 d51 o70 d55 b1100010 d125 x20 b101110 x20 b1001000 d97 d118 o145 x20 d97 o40 d103 d111 ' \
       'd111 x64 d32 o164 b1101001 x6d o145 x7e'

temp = []
temp = text.split(' ')

for i in range(len(temp)):
    if temp[i][0] == 'd':
        temp[i] = temp[i][1:]
        temp[i] = chr(int(temp[i], 10))
    elif temp[i][0] == 'x':
        temp[i] = temp[i][1:]
        temp[i] = chr(int(temp[i], 16))
    elif temp[i][0] == 'o':
        temp[i] = temp[i][1:]
        temp[i] = chr(int(temp[i], 8))
    elif temp[i][0] == 'b':
        temp[i] = temp[i][1:]
        temp[i] = chr(int(temp[i], 2))
print(''.join(temp))

```

#### affine
题目：  y = 17x-8 flag{szzyfimhyzd}
* 解题思路：题目提示了，是仿射密码（Affine cipher）。

#### Crack it($6$ shadow hash crack)
* 解题思路：使用john the ripper一步搞定，hashcat在超过32位的win hash上真不行。

#### RSA
* 解题思路：恶补了一下rsa的知识，ref：https://zhuanlan.zhihu.com/p/76017554
但是由于题目里N特别大，分解不好搞，查了一下，适合使用wiener attack的方法来解。
由于Rsactftools有更新，所以利用n,e,c的方式和之前的不一样了，使用方法参照 https://github.com/Ganapati/RsaCtfTool/issues/247 。

#### 来自宇宙的信号
* 解题思路：题目提示了，是银河字母。

#### 抄错的字符
提示：QWIHBLGZZXJSXZNVBZW
* 解题思路：看了评论，看来需要跑脚本，而且要base64解码。
这里结合base64的原理，四个字符代表三个字节，原题目有23位，也就是base64密文应该为`QWIHBLGZZXJSXZNVBZW=`的变化，四位四位的取出变形，解码观察是否可以成功解出来，并且能够组成一段话，或者写个脚本批量猜测变化fuzz，看能不能解出可阅读的字节。

#### Math&English
提示：英文、元音。
* 解题思路：好家伙，第一个提示直接给我思路整歪了，对着音标解了半天，结果其实作者暗示的是元音密码。。。
21.33.1.22=FLAG

#### 给你私钥吧
* 解题思路：逆向代码倒是不难，难点在如何获取私钥。
顺带补了一下RSA公私钥的知识点，不过RSA相关还是需要再多做做题。
这个晚点再补充，Sage下起来有些慢。
```
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP
import base64

flag='GSheKsD2r4g1baPF811ZVmZtzy3srPy7Qrr8uO7cDE/OD/dIc/48r9oGQyKp+XOor4nlHctqW9BzUEbGhX7vJ6aRlV/9RULhm6io1syVPsMaqvvVoEAspH/hk/NXOP383peNdXK6riu9hNTqvlenwgKx9iAuCaPUUsh1s5Vo6s/bORFMfGLVx4/B+b4pO8YubKe6lUNMLY8eUmgVEvISYcq7Fd1JEzlzA9/+ABXneTiFxVd3F6BdPHQyAbIWcE1fJFQao8MyZ6dBb764XuOKi8NvIt+VgVtVpBqtHzECYPYVpecUtqVvUiXhpimWMpvONFafOtObQ8GSN9FSmSwM4w=='
cipher_txt = base64.b64decode(flag)
f=open(r"privatekey.pem","r")
key = RSA.importKey(f.read())
cipher = PKCS1_OAEP.new(key)
message = cipher.decrypt(cipher_txt)
print(message)
```

#### 一段新闻
* 解题思路：这个真是毫无灵感一脸蒙蔽
看了WP才发现，是利用零宽字符转码，这个实在是有意思。

#### 你懂我的乐谱吗？
* 解题思路：一开始以为是乐谱加密/音符加密/Musical Cryptography。
然而这只给图没给文字版音符，怎么看怎么不能是让自己抄一遍。
看了WP（ref:https://blog.csdn.net/weixin_45696568/article/details/111413521），才发现是转简谱？
服了，不学音乐人不配解题。

#### funny number
* 解题思路：
```
str(flag).encode(), byteorder='little'

int a
a = str (b)[-175:]
b = int.from_bytes(c) << 10000  
c = str(flag).encode(), byteorder='little'
```
拆到这里，有两个难点，一个是byteorder='little'该如何反转得出flag，另一个是左移10000位的后175位该怎么反推。
关键词，模逆运算。
直接上大佬的代码吧，为数不多的看了WP也不会的题。
```
from gmpy2 import invert

n = pow(5,175)
p = 5845718273413614238047854434058144506973237928951593664100212455023083304425941087047510727554535833686148194478724602632928856425119454505382766186798132132909079456410238976

y = p // pow(2,175)
k = pow(2, 9825, n)
kinv = int(invert(k, n))
t = (y * kinv) % n
print(t)
#t=int.from_bytes(str(s).encode(), byteorder='little')

flag = bytes.fromhex(hex(t)[2:])[::-1]
print(flag)
```
