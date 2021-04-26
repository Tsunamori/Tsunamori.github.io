---
title: Notes_0 to 1 the road of CTFer
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
