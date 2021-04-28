---
title: 0 to 1 the road of CTFer 第一章 Web入门
toc: true
date: 2021-04-26 13:49:07
tags: Notes
categories: [100 Cyber security, 110 Bug bounty, 119 Notes]
---

# 第一章 Web入门

## 信息收集

### 敏感信息泄漏

#### git泄漏

1. 常规git泄漏：flag在源码内，可直接利用工具或脚本获取。
1. git回滚：flag被修改，但依然保存在commit记录里。
    * 利用`git reset --hard [版本号]`获取flag。
    * 更简单的方法是通过`git log-stat`查看每个commit修改了哪些文件，再用`git diff HEAD [commit-id]`比较当前版本和想看的commit之间的变化。
1. git分支：命令`git reflog`查看分支间切换的记录。从原url下载分支内容并恢复（目前的恢复分支记录需要下载分支HEAD并修改GitHacker.py代码实现自动恢复分支，先简单记录下来，后续看是否有其它更简单的方式。）
1. git stash：stash是对当前工作的“保存”，以备后续恢复。`git stash list`查看保存的列表，`git stash pop`恢复工作现场，`git stash show`显示做了哪些改动。
1. git index：index是暂存区，用于通过git add .命令暂存新建文件等待commit。使用`git checkout-index`命令恢复文件。
1. git泄漏的其他利用方式：泄漏的git中也可能有其他有用的信息。
    * .git/config文件夹中可能含有access_token信息，从而访问该用户的其它仓库。

#### SVN泄漏
SVN是源代码版本管理软件，如果存在泄漏，可以利用.svn/entries或wc.db文件获取服务器源码等信息。
* https://github.com/kost/dvcs-ripper
* Seay-svn（Win）

#### HG泄漏
.hg隐藏文件中包含代码和分支修改记录等信息。
* https://github.com/kost/dvcs-ripper

#### 总结
CTF线上赛往往会有重定向的问题，例如，只要访问`.git`便会返回403,此时访问`.git/config`，如果有文件内容返回，说明存在git泄漏，反之，一般不存在。而在SVN泄漏中，一般是在entries中爬取源代码，但有时会出现entries为空的情况，这时注意wc.db文件存在与否，便可通过其中的checksum在pristine文件夹中获取源代码。

### 敏感备份文件

#### gedit备份文件
在Linux下，用gedit编辑器保存文件后，当前目录下会生成一个`[文件名]~`的备份文件，访问这个文件即可获得文件内容。

#### vim备份文件
vim在意外退出时会生成一个备份文件，文件名格式为`.filename.swp`，再次生成备份文件时则为`.filename.swo`，第三次为`.filename.swn`，以此类推。恢复备份的办法是，先在当前目录下创建一个flag文件，再使用`vim-r flag`，即可恢复.flag.swp。

#### 常规文件
主要靠丰富字典。(dirsearch+自己收集字典)
* phpinfo()
* 备份文件后缀
```
    back.rar
    back.tar
    back.tar.gz
    back.zip
    backup.rar
    backup.tar
    backup.tar.gz
    backup.zip
    temp.rar
    temp.tar
    temp.tar.gz
    temp.zip
    web.rar
    web.tar
    web.tar.gz
    web.zip
    website.rar
    website.tar
    website.tar.gz
    website.zip
    www.rar
    www.tar
    www.tar.gz
    www.zip
    wwwroot.rar
    wwwroot.tar
    wwwroot.tar.gz
    wwwroot.zip
    index.php.bak
```

#### 总结
线上赛过程中，出题人往往会在线运维题目，有时会导致swp备份文件的生成。所以读者在比赛过程中可以编写实时监控脚本，对题目服务进行监控。

### Banner识别
在线上赛中，网站的Banner对解题有着十分重要的作用，选手往往可以通过Banner信息来获得解题思路。
如得知网站是用ThinkPHP的Web框架编写时，我们可以尝试ThinkPHP框架的相关历史漏洞。或者得知这个网站是Windows服务器，那么我们在测试上传漏洞时可以根据Windows的特性进行尝试。

#### 自行收集指纹库
* Github CMS指纹库
* 扫描器识别

#### 使用已有工具
* Wappalyzer（pip install python-Wappalyzer）：在data目录下，apps.json是其规则库，读者可以根据自己需求自由添加。

#### 总结
除了以上两种识别方式，我们还可以通过随意输入一些URL，观察404和302跳转页面中是否包含CMS及版本号。

## CTF中的SQL注入

### SQL注入（mysql）

#### 数字型注入
test.php?id=2 -> true。返回记录。
test.php?id=3-1 -> true，返回id=2的记录。
说明mysql对3-1进行了计算，从数字运算这个特征可以判断该注入点为数字型注入，输入点$_GET['id']附近没有引号包裹，可以直接输入SQL查询语句。

mysql查询出两行记录（union select联合查询注入）但PHP源码限制只能显示一行记录的办法：
    1. 在原有数据后面加上`limit 1,1`参数，显示查询结果的第二条记录。
    1. 指定id=-1或一个很大的值，使第一条数据无法被查询到。（常用）

#### 字符型注入
与数字型注入相比，只是在GET参数输入的地方包裹了单引号（少部分情况为双引号）让其变成字符串。
在MySQL中，等号两边如果类型不一致则会发生强制转换，当数字和字符串数据比较时，字符串将被转换为数字再进行比较。
test.php?id=3-2，无返回结果，证明注入点不是数字型。继续尝试test.php?id=2a，返回id=2的结果，证明存在字符型注入。

    1. 尝试使用单引号闭合前面的单引号，（插入查询语句），再用`--%20`或`%23`注释后面的语句，注意在URL框输入时，`-- `和`#`以及`''`要进行URL编码。
    1. 除了注释，也可以使用单引号来闭合后面的单引号，eg: test.php?id=2'union select xxx '

#### 布尔盲注
test.php?id=1'and'f'='a'，猜中则返回id=1内容
更快的方法为 test.php?id=1'and'f'<'n'，二分法。
如何获取每一位字符？利用mysql的substring(),mid(),substr()等。

#### 时间盲注
php?id=1'or sleep(10)

#### 报错注入
mysql会将语句执行后的报错信息输出。
updatexml在执行时，第二个参数应该为合法的XPATH路径，否则会在引发报错时输出传入的参数。
id='1' or updatexml(1,concat(0x7e,(select pwd from wp_user)),1)
updatexml()报错注入，ref: https://blog.csdn.net/qq_37873738/article/details/88042610

#### 堆叠注入
当目标开启多语句执行时，可以采用多语句执行的方式修改数据库结构和数据。

### 注入点

#### 注入点在select_expr
AS别名：id=(select%20pwd%20from%20wp_user)%20as%20title

#### 注入点在table_reference
仍可以用别名取出数据：SELECT title FROM (SELECT pwd AS title FROM wp_user)x;

在该两处的注入，如果注入点有反引号包裹，那么需要先闭合反引号。

#### 注入点在WHERE或HAVING后
代码：`$res = mysqli_query($conn, "SELECT title FROM wp_news WHERE id = ${_GET[id]}");`
现实中最常遇到的情况，要先判断有无引号包裹，再闭合前面可能存在的括号，即可进行注入来获取数据。

注入点在HAVING后的情况与之类似。

#### 注入点在GROUP BY或ORDER BY后
当遇到不是WHERE后的注入点时，先在本地MySQL中进行尝试，看语句后面能加什么，从而判断当前可以注入的位置，进而进行有针对的注入。

假设代码如下：`$res = mysqli_query($conn, "SELECT title FROM wp_news GROUP BY ${_GET['title']}");`
测试可得，title=id desc,(if(1,sleep(1),1))，可以利用时间注入获取数据。

```
事实上只要对输入值进行白名单比对，基本就能防御这种注入。
```

#### 注入点在LIMIT后
该注入比较简单，通过更改数字大小，页面会显示更多或更少的记录数。由于语法限制，LIMIT后只能是数字，在整个SQL语句没有ORDER BY关键字的情况下，可以直接使用UNION注入。另外可以根据SELECT语法，通过加入PROCEDURE来尝试注入（只适用于MySQL 5.6前的版本）

### INSERT注入
通常注入位于字段名或字段值的地方，且没有回显信息。

#### 注入点位于tbl_name
如果能够通过注释符注释后续语句，则可直接插入特定数据到想要的表内，如管理员表。

#### 注入点位于VALUES

### UPDATE注入

### DELETE注入
对id参数进行注入时，如果使WHERE后的值变为true，数据就会被删除。为了保证正常数据不会被干扰，通常使用`and sleep (1)`保证WHERE后的结果返回为false，让语句无法成功执行，后续步骤参考时间盲注。

## 注入防御及绕过思路

其它ref：https://blog.csdn.net/wy_97/article/details/78085664

### 关键字替换或匹配拦截

#### 过滤空格
替代空格的空白符有：`%0a, %0b, %0c, %0d, %09, %a0`（均为URL编码，%a0在特定字符集才能利用）和`/**/`组合、括号等。

#### 将SELECT替换成空
可以采用嵌套的方式，如SESELECTLECT。

#### 大小写匹配
在MySQL中，关键字是不区分大小写的，如果只匹配了SELECT就可以通过大小写混写的方式绕过。

#### 正则匹配
正则匹配关键字`\bselect\b`可以用形如`/*!50000select*/`的方式绕过。

#### 替换了单引号或双引号，没有过滤反斜杠
`id='a\' AND title = 'OR sleep(1)#'`
反斜杠转义了预置的单引号，实现绕过。

### 逃逸引号

#### 编码解码
当用户输入待转义函数时，如果数据处于编码状态则引号无法被转义。
同样的情况也发生在加密/解密、字符集转换的情况。
* 宽字节注入

#### 意料之外的输入点
被遗漏的可控点，例如PHP中，上传的文件名、http header、$_SERVER['PHP_SELF'] 等变量。

#### 二次注入
由于开发者信任数据库中取出的数据是无害的，从而导致二次注入漏洞的出现。
*  用户名为`admin'or'1`

#### 字符串截断
在标题、抬头等位置，开发者可能限制其字符不能超过x个字符，超过就会被截断。
假如攻击者输入`aaaa'`，自动转义为`aaaa\'`，由于长度限制被截取为`aaaa\`，正好转义预置的单引号，实现注入。

### 注入应用
* 在有写文件权限的情况下，直接用INTO OUTFILE或DUMPFILE向Web目录写文件，或写文件后结合文件包含漏洞实现代码执行。ref:https://baijiahao.baidu.com/s?id=1650711425682220792&wfr=spider&for=pc
* 在有读文件权限的情况下，用load_file()函数读取网站源码和配置信息，获取敏感数据。
* 提升权限，绕过登录，添加用户，调整用户权限。
* 通过数据，控制如缓存、模板等文件内容来获取权限，或者删除、读取某些关键文件。
* 在可以执行多语句的情况下，控制整个数据库，包括控制任意数据、任意字段长度等。
* 在SQL Server这类数据库中可以直接执行系统命令。

### 总结
实际比赛中，应根据不同的SQL服务器类型查找相关资料，通过fuzz得出被过滤的字符、函数、关键词等，在文档中查找未过滤的替代，完成绕过。
* 联系靶场：github-sqli-labs

## 任意文件读取漏洞
