---
title: CTF PHP-RCE
toc: true
date: 2021-04-20 16:22:46
tags: CTF
categories: [100 Cyber security, 120 CTF, 121 Web]
---

### 文件包含
1.  文件包含命令执行 https://xz.aliyun.com/t/5535
    *  https://blog.csdn.net/nai_kai/article/details/106805220
1.  PHP伪协议（php://） https://www.jianshu.com/p/0a8339fcc269
1.  文件包含漏洞利用 https://blog.csdn.net/qq_32393893/article/details/110228864
1.  远程文件包含漏洞（Remote File Inclusion RFI）:
    *  PHP的配置选项allow_url_include为ON的话，则include/require函数可以加载远程文件，这种漏洞被称为"远程文件包含漏洞(Remote File Inclusion RFI)"。
    *  allow_url_fopen = On 是否允许打开远程文件; allow_url_include = On 是否允许include/require远程文件
    *  远程文件包含webshell https://www.jianshu.com/p/be68cf9be911
1.  RCE find the file by name in linux
    *  https://www.plesk.com/blog/various/find-files-in-linux-via-command-line/
    *  https://www.linux.com/topic/desktop/how-search-files-linux-command-line/
1.  在不能直接获取文件时，使用php://filter/read=convert.base64-encode/resource=test.php绕过获取文件内容
    * 如读取文件限制为：
    ```
    <?php
	error_reporting(0);
	if(!$_GET[file]){echo '<a href="./index.php?file=show.php">click me? no</a>';}
	$file=$_GET['file'];
    if(strstr($file,"../")||stristr($file, "tp")||stristr($file,"input")||stristr($file,"data")){
		echo "Oh no!"; //不能直接读取绝对路径
		exit();
	}
  include($file); //include不能包含自身，会死循环，故拒绝访问。
  ```
1. 文件包含漏洞总结 https://blackdn.github.io/2020/04/04/CTF-File-Inclusion-2020/

### 命令执行绕过
1.  https://blog.csdn.net/u014549283/article/details/81783164
1.  https://zhuanlan.zhihu.com/p/127047303
1.  https://www.codenong.com/cs110213257/
1.  https://www.anquanke.com/post/id/208398
1.  ${HOME:0:1} = / (获取目录分割符绕过过滤)
1.  https://www.cnblogs.com/Tkitn/p/11661017.html
1.  https://blog.csdn.net/qq_45927819/article/details/109671655
1.  管道符（; | || & &&）过滤绕过：换行符%0a
1.  空格绕过：%20(目前做题最好使的一个绕过，<>经常不成功)
1.  关键词绕过：引号需要每个字母之间添加引号'或"，加`\`的话只需要加一个,cat可以用tac代替。
1.  文件读取不能用cat的替代：show_source('flag.php')
1.  Eval命令绕过利用，eval()内参数数目不限，彼此之间用分号隔开。
    *  eval( "var_dump($a);"); -> eval( "var_dump(1),system('ls');");
1.  Assert()命令执行漏洞：
1.  var_dump(`vi flag.php`)
1.  不允许输入标点符号，只允许数字和大小写，读取文件：$$args -> $args = GLOBALS

### Webshell RCE 绕过
1. https://xz.aliyun.com/t/8354
1. https://www.leavesongs.com/PENETRATION/webshell-without-alphanum-advanced.html
1. 不包含字母和数字： https://cloud.tencent.com/developer/article/1186230

### 目录遍历
1. ../../etc/passwd
    * https://www.freebuf.com/vuls/247620.html

### file_get_contents()绕过
```
此函数可以获取本地的内容，也可以支持HTTP、FTP等协议远程抓取内容。

当使用该函数打开某个文件时
$result=file_get_contents($_GET['xxx'])

可以通过在url里使用 ?xxx=data:text/plain,(url编码的内容)
这时$result的值为url编码的内容

或者也可以在url里使用?xxx=php://input
然后将要赋值的数据写入POST里也可达到上述结果
```
