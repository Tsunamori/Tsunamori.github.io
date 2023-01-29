---
title: File upload bypass
toc: true
date: 2021-04-14 13:57:46
tags: Summary
categories: [100 Web security, 110 Bug bounty, 111 Ideas/WP]
---

1. 文件上传漏洞总结 https://www.yuque.com/jxswcy/ctfnotebook/qhc3xt
1. File upload bypass
    * bypassing normal extension
        sth.php.jpg or sth.jpg.php
    * bypassing the magic byte validation
        polyglots
1.  https://www.codenong.com/cs106601908/
1.  .htaccess https://writeup.ctfhub.com/Skill/Web/%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0/igWAbQLuV5hDSjGssDxLpa.html
1.  %00截断 https://www.cnblogs.com/miansj/p/13996032.html
    * php版本小于5.3.4
    * php的magic_quotes_gpc为OFF状态
1.  双写绕过 https://cloud.tencent.com/developer/article/1376933

#### 中间件文件上传漏洞
ref:http://gv7.me/articles/2018/make-upload-vul-fuzz-dic/
1. IIS：
    1. IIS6.0文件解析 xx.asp;.jpg
    2. IIS6.0目录解析 xx.asp/1.jpg
    3. IIS 7.0畸形解析 xxx.jpg/x.asp
1. Apache:
    1. /1.php%0A(CVE-2017-15715)(2.4.0 to 2.4.29)
    1. 未知后缀 test.php.xxx
1. Nginx:
    1. 访问连接加/xxx.php test.jpg/xxx.php
    1. 畸形解析漏洞 test.jpg%00xxx.php
    1. CVE-2013-4547 test.jpg(非编码空格)\0x.php
1. Tomcat:(限制在windows操作系统)
    1. xxx.jsp/
    1. xxx.jsp%20
    1. xxx.jsp::$DATA

#### 文件上传结合文件包含利用
ref：https://www.secpulse.com/archives/71246.html
1. 当php语句中有如下内容时，上传图片马并运行路径`index.php?page=/upload/logo.jpg`（page、file）
```
if (isset($_GET[page])) {
include $_GET[page];
```

#### python里读取文件路径上一级路径及文件的方法
https://blog.csdn.net/niedongri/article/details/80662158
获取上一级路径内文件：os.system('cat ../flag')
echo或许也可以替代。
