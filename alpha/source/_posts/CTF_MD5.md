---
title: CTF MD5
toc: true
date: 2021-04-25 10:30:06
tags: CTF
categories: [100 Cyber security, 120 CTF, 121 Web ]
---


#### MD5()函数数组绕过
1.  
```
<?

include_once “flag.php”;
ini_set(“display_errors”, 0);
$str = strstr($_SERVER[‘REQUEST_URI’], ‘?’);//返回$_SERVER[‘REQUEST_URI’]内从‘?’后开始的值。
$str = substr($str,1);
$str = str_replace(‘key’,”,$str);
parse_str($str);
echo md5($key1);

echo md5($key2);
if(md5($key1) == md5($key2) && $key1 !== $key2){
echo $flag.”取得flag”;
}
?>
```
绕过方法：
*  对key关键词的过滤可以用kekeyy绕过，从而传递key1&key2进行比较。
*  md5()函数无法处理数组，如果传入的为数组，会返回NULL，所以两个数组经过加密后得到的都是NULL,也就是相等的。
*  利用==比较漏洞,如果两个字符经MD5加密后的值为 0exxxxx形式，就会被认为是科学计数法，且表示的是0*10的xxxx次方，还是零，都是相等的。下列的字符串的MD5值都是0e开头的：
`QNKCDZO`
`240610708`
`s878926199a`
`s155964671a`
`s214587387a`
`s214587387a`

ref: https://blog.csdn.net/zpy1998zpy/article/details/80582974
