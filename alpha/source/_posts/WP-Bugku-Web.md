---
title: WP-BugKu-Web
toc: true
date: 2021-05-08 11:43:54
tags: [CTF, WriteUp]
categories: [100 Cyber security, 120 CTF, 121 Web ]
---

#### Web 18
提示：题目刷新几次后有提示，需要计算数值并post value。

*  考点：爬虫复习、利用eval()直接计算string内的数字公式、利用同一个Session进行get和post请求，防止网页动态刷新计算值失效、post请求中data注意格式为{‘somevtext’：’somevalue’}、python和PHP的计算方式不太一样，可能存在计算偏差导致得不到flag，需要重复发送几次。
    ```
import requests
from bs4 import BeautifulSoup

url = 'http://114.67.246.176:14925/'
s = requests.session()
r = s.get(url)
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

#### Web 19
提示： OK ,now you have to post the margin what you find

* 解题思路：刷新几次之后没有发现变化，f12看network请求，在response包header内看到base64加密的flag，解码后为‘跑的还不错，给你flag吧: MjE0Nzg1’，一开始并没有意识到后面是base64的密文，直到一次运行爬虫发现密文有=号结尾。根据其他人的解读，说margin是数字所以提示了有两次base64解密，完全没有意识到这一层，我只猜到了post数据时数据格式为{‘margin’:xxx}，不得不说这提示的语法不太行，应该删掉what，这个着实困扰了。
    这个解开之后就简单很多了，另一个难点是在py3里string无法直接用base64解密，需要先转换成ascii。以及解密之后的返回值为bytes，需要转换成utf-8格式再进行后续删减处理，不然有`b’‘`等噪音。
    ```
    import base64
    import requests

    url='http://114.67.246.176:19877/'
    s=requests.session()

    r=s.get(url).headers.get('flag')
    r1=base64.b64decode(r.encode('ascii'))
    r2=r1.decode("utf-8")
    r3=r2[len(r2)-8:len(r2)]
    r4=base64.b64decode(r3.encode('ascii')).decode("utf-8")

    print(s.post(url, data={'margin':r4}).text)
    ```

#### Web 23
题目：
```
<?php
highlight_file('2.php');
$key='flag{********************************}';
$IM= preg_match("/key.*key.{4,7}key:\/.\/(.*key)[a-z][[:punct:]]/i", trim($_GET["id"]), $match);
if( $IM ){
 die('key is: '.$key);
}
?>
```

* 解题思路：基本上难点都在这句正则上了，外加一个trim()去掉输入值前后的空白字符。解读一下php语句，就是当id的输入值符合正则时，退出（die）并返回key。2.php在此处仅为含有该php语句的文件（highlight_file），与输入值无关。跟着这道题试验了好几个找到的在线正则工具，发现还是regex101好用。
  `/key.*key.{4,7}key:\/.\/(.*key)[a-z][[:punct:]]/i`，`{4,7}`表示重复前面部分4～7次，`[:punct:]`为所有的特殊字符。
  payload：`keykeykkeykey:/k/keya[:punct:]`

#### Web 24
题目：
```
<?php
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];
    $v3 = $_GET['v3'];
    if($v1 != $v2 && md5($v1) == md5($v2)){
        if(!strcmp($v3, $flag)){
            echo $flag;
        }
    }
}
?>
```

* 解题思路：似曾相识的MD5相等题，此次应掏出MD5绕过笔记。strcmp(str1,str2),如果str1小于str2返回<0；如果str1大于str2返回>0；如果两者相等，返回0。因为是if(!xxx)返回flag，所以v3和flag相等。搜索strcmp绕过，可知当传入的类型非期望类型时，即传入值非string时，发生错误，返回0。如：数组,`?a[]=2`

#### Web 25
提示：SQL约束攻击

* 解题思路：点开链接，发现是一个后台登录站点，先放着不管，查一下提示给的SQL约束攻击。内容也算似曾相识，之前在bug bounty的hint里面见过，也就是利用用户名长度限制，在用户名最后加上多个空白字符以及末尾一个用于绕过用户名是否已注册的对比的任意字符，那么在存储时，数据库仅存储前X位字符，导致攻击者可以取代登录目标用户。ref：https://www.freebuf.com/articles/web/124537.html
  知道了就简单了，思路是先盲猜管理员用户名，然后通过SQL约束攻击注册新帐号，取代替换原管理员帐号的密码。

#### Web 26
提示：are you from google?

* 解题思路：这个提示已经很明显了，直接包头加转发（referer），表示自己是从google来的就行。突然在想x-forward-for为啥不行，查了一下XFF必须是IP地址。

#### Web 27
提示：
    1. please input a
    2. md5 collision

* 解题思路：MD5碰撞，题面只有一句please input a，盲猜是输入a和某个值碰撞，那么首先试试输入数组报错法，回显`false!!`，起码证明思路应该是对的，重新查了查MD5碰撞，猜测题目是强类型不能绕过。但不知道另一个对比值要怎么做真实碰撞呢？
  好家伙，看了看提示，发现是没给对比值源码。。。行8

#### Web 28
提示：请从本地访问。

* 解题思路：好的看到本地访问又稳了，直接开链接搞XFF。题目不但似曾相识还莫名的变简单了。。。
  我一般是上burp，不过在评论里看到一个`curl -H "X-Forwarded-For: 127.0.0.1" http://114.67.246.176:18832/`，码住。