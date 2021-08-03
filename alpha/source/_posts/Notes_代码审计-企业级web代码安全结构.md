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
1. allow_url_include(是否允许包含远程文件)：在该配置为on的情况下，可以直接包含远程文件，当存在include(`$var`)且$var可控的情况下，可以直接控制$var变量来执行PHP代码。allow_url_include在PHP5.2.0后默认设置为off，配置范围为PHP_INI_ALL。与之类似的配置有allow_url_fopen，配置是否允许打开远程文件，但安全隐患没有前者大。
    代码实例：(实验环境php5.2.17，payload：a=http://127.0.0.1:80/test/info.txt，info.txt内容为`<?php phpinfo();?>`)
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
1. magic_quotes_runtime(魔术引号自动过滤)：过滤方式同样为加反斜杠，但和magic_quotes_gpc的处理对象不一样。magic_quotes_runtime只对从数据库或文件中获取的数据进行过滤，magic_quotes_runtime在PHP5.4之后被取消，配置范围是PHP_INI_ALL。但该参数仅对部分函数有作用，某些情况下可以被绕过。
    代码实例：
    ```
#1.txt
1'2"3\4

<?php
ini_set("magic_quotes_runtime","1");
echo file_get_contents("1.txt");
    ```
1. magic_quotes_sybase(魔术引号自动过滤)：用于自动过滤特殊字符，当设置为on时，会覆盖magic_quotes_gpc=on的配置（使gpc=on失效）。与gpc的共同点是处理对象一致（GET、POST、Cookie)，但该参数仅转义空字符以及把单引号变成双引号，使用率比gpc低。配置范围为PHP_INI_ALL，在PHP5.4.0中移除。（代码实例与gpc相同）
1. safe_mode（安全模式）：是PHP内嵌的一种安全机制，配置范围为PHP_INI_SYSTEM，PHP5.4之后被取消（取消原因是，PHP开发者认为在PHP语言机制上试图解决安全问题是一件不合适的事情，虽然safe_mode在一定程度上对共享主机有效，但同时也带来了不少误报，与其在PHP上解决权限安全问题，不如使用linux默认的权限限制机制或其它层级的解决办法）。该参数效果为，所有文件操作函数都会受到限制，非文件所有者不能对该文件进行操作（如include()），如果有一些脚本文件放在非Web服务启动用户所有的目录下，需要利用include等函数进行加载，可以使用safe_mode_include_dir来配置可包含的路径。此外，通过函数popen()、system()以及exec()等函数执行命令或程序会提示错误，如果需要使用外部脚本，可以集中存放，然后用safe_node_exec_dir来指向存放目录。
    代码实例：
    ```
# echo `whoami`; 执行命令失败的回显提示
Warning: shell_exec() [function, shell_exec]: Cannot execute using backquotes in Safe Mode ...
    ```
1. open_basedir（PHP可访问目录）：用于限制PHP只能访问哪些目录，通常只需要设置Web文件目录即可，如果需要加载外部脚本，也需要把所在路径加入该指令中，多个目录以分号分割。需要注意，指定限制实际上是前缀而不是目录名，如配置open_basedir=/www/a，那么/www/a和/www/ab都可以访问，所以为了避免该现象发生，需要用斜线结束路径名，如/www/a/。当参数激活，执行脚本访问其它文件时都需要验证文件路径，所以会影响执行效率。该指令配置范围在PHP<5.2.3时是PHP_INI_SYSTEM，在PHP>=5.2.3时是PHP_INI_ALL。
1. disable_functions(禁用函数)：使用该指令来禁止敏感函数的使用，使用本指令时，需把dl()函数也添加进禁用列表，否则攻击者可以利用dl()函数价值自定义的PHP扩展突破该指令的限制。指令范围为php.ini，配置禁用函数时使用逗号分割函数名。
1. display_errors和error_reporting错误显示：display_errors用于表明是否显示PHP脚本内部错误，生产环境中建议关闭，在开启时，可以通过设置error_reporting来设置错误显示的级别。配置范围均为PHP_INI_ALL。

### 审计辅助与漏洞验证工具

#### 代码编辑器
1. Notepad++
1. UltraEdit（文件对比）
1. Zend Studio（PHP集成开发环境）

#### 代码审计工具
1. Seay源代码审计系统
1. RIPS

#### 漏洞验证辅助
1. Burp
1. 浏览器扩展：Hackbar, Firebug, Live HTTP Headers, Modify
1. 编码转换及加解密工具：Seay代码审计系统自带的编码功能，Burp自带的decoder，超级加解密转换工具
1. 正则调试工具：Seay自带的正则调试功能，灵者正则调试
1. SQL执行监控工具：Seay mysql监控

## 漏洞发现与防范

### 通用代码审计思路

#### 敏感函数回溯参数过程
根据敏感函数来逆向追踪参数的传递过程，使用较多，因为大多数漏洞都是由于函数使用不当造成的。非函数使用不当的漏洞，如SQL注入，也有一些特征，如Select、Incert等，结合From和Where等关键字判断是否为一条SQL语句，通过对字符串的识别分析，就能判断该SQL语句参数有没有使用单引号过滤，或者根据经验判断。如HTTP头里面的HTTP_CLIENT_IP和HTTP_X_FORWORDFOR等获取到的IP地址常直接拼接到SQL语句中，且由于它们是存在于`$_SERVER`变量中不受GPC的影响，那么就可以查找这两个参数关键字快速寻找漏洞。
该方法的优点是定向挖掘、高效、高质量，缺点是对整体框架了解不够深入，定位利用点会花费时间，另外无法覆盖逻辑漏洞。

#### 通读全文代码
在企业中做自身产品代码审计时，我们需要了解整个应用的业务逻辑以获取更多漏洞。
通读全文代码时，首先要看程序的大体代码结构，如主目录有哪些文件，模块目录有哪些文件，插件目录有哪些文件，还要注意文件大小、创建时间。根据文件命名可大致了解该程序实现哪些功能，核心文件是哪些。
在看程序目录结构时，要特别注意以下几个文件：
1. `函数集文件`，通常命名中包含functions或者common等关键字，这些文件内是一些公共函数，提供给其它文件统一调用，所以大多数文件会在文件头包含其它文件。寻找这些文件的一个技巧就是打开index.php或一些功能性文件，在头部一般都能找到。
1. `配置文件`，通常命名中包含config关键字，包括Web程序运行必须的功能性配置选项及数据库等配置信息，从该文件中可以了解程序的小部分功能，另外看这个文件时注意观察配置文件中参数值是用单引号还是双引号，如果是双引号，则很可能存在代码执行漏洞。（如利用PHP可变变量（$$a）的特性执行代码，ref：https://www.cnblogs.com/Cl0ud/p/12336834.html）
1. `安全过滤文件`，该文件关系到挖掘到的可疑点能否利用，通常命名中有filter、safe、check等关键字。这类文件主要作用是针对参数进行过滤，比较常见的是针对SQL注入和XSS过滤，还有文件路径、执行的系统命令的参数，其它相对少见。而目前大多数应用会在程序入口循环对所有参数使用addslashes()进行过滤。
