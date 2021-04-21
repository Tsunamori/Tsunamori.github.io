---
title: RCE
toc: true
date: 2021-04-20 16:22:46
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

1.  Eval command
    *  read files eg: test.com/?a=system('ls /')
1.  文件包含命令执行 https://xz.aliyun.com/t/5535
    *  https://blog.csdn.net/nai_kai/article/details/106805220
1.  PHP伪协议（php://） https://www.jianshu.com/p/0a8339fcc269
1.  远程文件包含漏洞（Remote File Inclusion RFI）:
    *  PHP的配置选项allow_url_include为ON的话，则include/require函数可以加载远程文件，这种漏洞被称为"远程文件包含漏洞(Remote File Inclusion RFI)"。
    *  allow_url_fopen = On 是否允许打开远程文件; allow_url_include = On 是否允许include/require远程文件
    *  https://www.jianshu.com/p/be68cf9be911
1.  RCE find the file by name in linux
    *  https://www.plesk.com/blog/various/find-files-in-linux-via-command-line/
    *  https://www.linux.com/topic/desktop/how-search-files-linux-command-line/
1.  命令执行绕过：
    *  https://blog.csdn.net/u014549283/article/details/81783164
    *  https://zhuanlan.zhihu.com/p/127047303
    *  https://www.codenong.com/cs110213257/
    *  https://www.anquanke.com/post/id/208398
    *  ${HOME:0:1} = / (获取目录分割符绕过过滤)
    *  https://www.cnblogs.com/Tkitn/p/11661017.html
    *  https://blog.csdn.net/qq_45927819/article/details/109671655
    *  管道符（; | || & &&）过滤绕过：换行符%0a
    *  关键词绕过：引号需要每个字母之间添加引号'或"，加`\`的话只需要加一个
