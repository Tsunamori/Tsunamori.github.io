---
title: PHP_CTF
toc: true
date: 2021-04-23 09:51:12
tags: CTF
categories: [100 Cyber security, 120 CTF, 121 Web]
---

### PHP 变形绕过
1. `==`比较双方都是数字或数字字符串（a string which can be interperted as int or float）
    * var_dump(0 == "a"); // 0 == 0 -> true
    * var_dump("1" == "01"); // 1 == 1 -> true
    * var_dump("10" == "1e1"); // 10 == 10 -> true
    * var_dump(100 == "1e2"); // 100 == 100 -> true
    * 0=='0' //true
    * 0 == 'abcdefg' //true
    * 0 === 'abcdefg' //false
    * 1 == '1abcdef' //true

### PHP Eval
1. eval()内参数数目不限，彼此之间用分号隔开。

### PHP 环境变量和系统变量
1. 常用环境变量和系统变量：
    * https://blog.csdn.net/liubing300/article/details/6883715
    * $GLOBALS:包含了全部变量的全局组合数组。
