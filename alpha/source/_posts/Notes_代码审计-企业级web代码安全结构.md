---
title: 代码审计 企业级web代码安全结构
toc: true
date: 2021-07-17 10:05:28
tags: Notes
categories: [100 Cyber security, 140 Code review 代码审计, 149 Notes]
---

苦于代码审计能力偏弱，调研一番发现这本书或许有所帮助。

## 第一部分 代码审计前的准备

### 代码审计环境搭建
1. wamp/wnmp: WAMP（Windows下的Apache+Mysql/MariaDB+Perl/PHP/Python），WNMP（Windows下的Nginx+Mysql+PHP）
1. lamp/lnmp：将上述环境安装在Linux中。

#### PHP_INI_*常量的定义
1. PHP_INI_USER：该配置选项可在用户的PHP脚本或Win注册表中设置。
1. PHP_INI_PERDIR：该配置选项可在php.ini. .htaccess或httpd.conf中设置。
1. PHP_INI_SYSTEM：该配置选项可在任何地方设置。
1. PHP_INI_ALL：该配置选项可在任何地方设置。
1. php.ini only：该配置选项可仅可在php.ini中配置。

#### 会影响PHP脚本安全的配置列表及核心配置选项
1. register_globals(全局变量注册开关)：该选项在on的情况下，会将用户GET/POST等方式提交上来的参数注册成全局变量并初始化值为参数对应的值，使提交参数可以直接在脚本中使用。register_globals在PHP版本小于等于4.2.3时设置为PHP_INI_ALL，从PHP5.3.0起被废弃，在PHP5.4.0中被移除。
    代码实例：(实验环境php5.2.17,在php.ini中添加register_globals = On)
    ```
<?php
if($user=='admin'){
  echo 'true';
}
    ```
1. allow_url_ianclude(是否允许包含远程文件)：在该配置为on的情况下，可以直接包含远程文件，当存在include($var)且$var可控的情况下，可以直接控制$var变量来执行PHP代码。allow_url_ianclude在PHP5.2.0后默认设置为off，配置范围为PHP_INI_ALL。与之类似的配置有allow_url_fopen，配置是否允许打开远程文件，但安全隐患没有前者大。
    代码实例：
    ```
<?php
include $_GET['a'];
    ```
1. magic_quotes_gpc（魔术引号自动过滤）：该参数在不存在编码或其他特殊绕过的情况下，可以使很多漏洞无法利用。当该参数被开启时（选项设置为on），会自动在GET、POST、COOKIE变量中的单引号（‘）、双引号（“）、反斜杠（\）及空字符（NULL）的前面加上反斜杠（\），但在PHP5中magic_quotes_gps并不会过滤$_SERVER变量，导致很多类似client-ip、referer一类的漏洞能够利用。PHP5.3之后不推荐使用该参数，PHP5.4之后被取消。在PHP版本小于4.2.3时，配置范围是PHP_INI_ALL;在PHP版本大于4.2.3时，是PHP_INI_PERDIR。
    代码实例：(测试?a=1')
    ```
<?php
echo $_GET['a'];
    ```
