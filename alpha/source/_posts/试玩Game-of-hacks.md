---
title: Game of hacks 试玩
toc: true
date: 2021-07-15 16:44:14
tags: 代码审计
categories: [学习笔记]
---

### 前言
从Awesome-hacking_Cyber-skills看到的练习网站，粗略看了一下描述是关于代码审计的，正好一直在考虑提升一下代码审计的技能，最近就来试玩一下，顺便记录WP。

### 准备工作
一开始有难度选项，分别是beginner、intermediate和advanced。嗯，十分正常的分类。另外要选代码语言，一共十种，我比较在意的Android、Java、PHP、Python、.NET都在，令人满意。
看了题目大概是每次随机抽五道题+计时+提供选项，所以WP就先简单记一下代码，最后整理思路。

### PHP 代码审计

#### Beginner

```
int i;
char inLine[64];
cin >> inLine; // 将cin的二进制码向右移动inLine位（或将cin十进制除以2 inLine次）,但是在这里没有用到。
i = atoi (inLine); // atoi()：字符串转换整数
sleep(i);
```


```
Dim myPassWord As String
myPassWord = "12345"
```
use of hardcoded password

```
Private Sub cmdRunNotePad_Click()
Dim str As String
MyVar = window.Text()
Sleep myVar+1
dblNotePadID = Sleep(myVar)
End Sub
```


```
<?php
$dn = $_GET['host'];
$filter="(|(sn=$person*)(givenname=$person*))";
$justthese = array("ou", "sn", "givenname", "mail");
$sr=ldap_search($ds, $dn, $dn, $justthese);
$info = ldap_get_entries($ds, $sr);
echo $info["count"]." entries returned
";?>
```
LDAP injection

```
String url = req.getRequestURL().toString();
RequestDispatcher disp = req.getRequestDispatcher(url.substring(url.lastIndexOf(req.getContextPath() + "/")
+ req.getContextPath().length()));
disp.forward(request, response);
```
