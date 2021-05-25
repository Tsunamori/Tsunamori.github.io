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
for i in range(17):
    tmp += s1[i]+s2[i+2]+s3[i+4]+s4[i+6]+s5[i+8]+s6[i+10]+s7[i+12]+s8[i+14]+s9[i+16]

def cut(obj, sec):
    str_list = [obj[i:i+sec] for i in range(0,len(obj),sec)]
    print(str_list)
    return str_list

l1=cut(tmp,17)
for i in l1:
    print (i)
```
然后用专用的工具AZdecrypt（https://m.majorgeeks.com/files/details/azdecrypt.html）解密。
