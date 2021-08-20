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
1. `index文件`，是一个程序的入口文件，通过阅读该文件可大致了解整个程序的架构、运行流程、包含的文件，以及核心文件有哪些。而不同的目录的index文件也有不同的实现方式，最好先将核心目录的index文件都简单读一遍。
学习代码审计前期建议先下载一些小应用来读，积累经验后，再去读开源框架。

#### 根据功能点定向审计
先简单黑盒测试一下，再通过发现的容易出问题的功能去阅读该功能点的源码，提高审计速度。
1. 文件上传功能：任意上传、SQL注入
1. 文件管理功能：任意文件操作、XSS漏洞
1. 登录认证功能：任意用户登录
1. 找回密码功能：验证码爆破、验证凭证算法

### 漏洞挖掘与防范（基础篇）

#### SQL注入

##### 挖掘经验
常出现在登录页面、获取HTTP头（user-agent/client-ip等）、订单处理等业务相对复杂的地方，登录页面注入大多出现在HTTP头的client-ip和x-forward-for，用于记录登录IP地址。另外在订单系统内，由于订单涉及购物车等多个交互，经常会发生二次注入，通读代码时可着重关注这几个地方。
1. 普通注入：指最容易利用的SQL注入漏洞，有int型和string型，在string型注入中需要使用单或双引号闭合。数据库操作存在一些关键字，如select from、mysql_connect、mysql_query、mysql_fetch_row等，查询方式还有update、incert、delete，只需要在白盒审计中查找这些关键字即可定向挖掘SQL注入。
1. 编码注入：程序在进行一些操作前经常会进行编码处理，而做编码处理的函数可能会存在问题。通过输入转码函数不兼容的特殊字符，即可导致输出字符变成有害数据，在SQL注入里，最常见的编码注入是MySQL宽字节以及urldecode/rawurldecode函数导致的。
    * 宽字节注入：使用PHP连接MySQL的时候，当设置`set character_set_client=gbk`时会导致一个编码转换的注入问题，当存在该漏洞时，注入参数里带入`%df%27`，即可把程序中过滤的`\(%5c)`吃掉。而通常都不是直接设置`set character_set_client=gbk`，而是设置`SET NAMES ‘gbk’`，同样存在漏洞。官方建议是使用mysql_set_charset来设置编码，只要在后面合理的使用mysql_real_escape_string还是可以解决该漏洞的。对宽字节注入的挖掘方法比较简单，搜索`SET NAMES`、`character_set_client=gbk`、`mysql_set_charset('gbk')`。该漏洞的解决方法如以下三种，比较推荐一和三：
        * 在执行查询前先执行`SET NAMES 'gbk', character_set_client=binary`
        * 使用mysql_set_charset('gbk')设置编码，然后使用mysql_real_escape_string()过滤。
        * 使用pdo方式，在PHP5.3.6及以下版本中需要设置`setAttribute(PDO::ATTR_EMULATE_PREPARES,false);`，来禁用prepared statements的仿真效果。
    * 二次urlencode注入：只要字符被进行转换就有可能产生漏洞。现在的Web程序大多会进行参数过滤，通常使用addslashes()、mysql_real_escape_string()、mysql_escape_string()函数或者开启GPC的方式来防止注入，也就是给单引号、双引号、反斜杠（\）和NULL加上反斜杠转义。如果某处使用了urldecode或者rawurldecode函数，则会导致二次加码生成单引号而引发注入。该漏洞可以通过搜索urldecode和rawurldecode函数来挖掘。

##### 漏洞防范
1. gpc/runtime魔术引号：通常数据污染有两种方式，一种是应用被动接收参数，另一种是主动获取参数。利用magic_quotes_gpc和magic_quotes_runtime可以防止部分SQL注入（对int型注入没有太大作用）
1. 过滤函数和类：有两种使用场景，一种是程序入口统一过滤，框架程序使用这种方式比较多，另一种是在程序进行SQL语句运行前使用，除了PHP内置的一些过滤单引号等函数外，还有一些开源类过滤union、select等关键字。
    * addslashes函数：过滤单引号、双引号、反斜杠以及空字符NULL，大多被用在程序入口，判断如果没有开启GPC则使用该函数进行过滤。不过它的参数必须是string类，所以可能会存在通过数组绕过的漏洞。
    * mysql_[real_]escape_string函数：这两个函数都是对字符串进行过滤，只存在于大于PHP4.03的版本，[`\x00`]、[`\n`]、[`\r`]、[`\`]、[`'`]、[`"`]、[`\xla`]会受到影响。两个函数唯一不一样的地方在于mysql_real_escape_string接受的是一个连接句柄并根据当前字符集转移字符串，推荐使用。
    * intval等字符转换：上述方式在int类型注入时效果不会，比如可以通过报错或盲注等方式来绕过，这时候就要用到intval函数了。intval的作用是将变量转换成int类型，这里举例intval是要表达一种利用参数类型白名单的方式来防止漏洞，对应的还有很多如floatval等。
1. PDO prepare预编译：通过预编译的方式来处理数据库查询。当PHP版本<5.3.6时，使用PHP本地模拟prepare再把完整的SQL语句发送给MySQL服务器，且使用set names 'gbk'时，仍然存在宽字节SQL注入，因为PHP和MySQL编码不一致。正确的写法应该是使用ATTR_EMULATE_PREPARES来禁用PHP本地模拟prepare。

#### XSS漏洞

##### 挖掘经验
挖掘XSS漏洞关键在于寻找没有被过滤的参数，且这些参数传入至输出函数。常用输出函数列表如下：`print、print_r、echo、printf、sprintf、die、var_dump、var_export`，寻找带有变量的这些函数即可。另外在代码审计中，浏览器环境对XSS漏洞利用影响非常大。通读代码时可多关注各处设置资料、文章发表、留言等富文本区域，这种地方存在的XSS通常是存储型的。

##### 反射型 XSS
直接通过外部输入在浏览器端输出触发，该种漏洞比较容易通过扫描器黑盒审计发现。白盒审计中，只需要寻找带有参数的输出参数，根据输出参数对输出内容回溯输入参数，观察有没有经过过滤。

##### 存储型XSS
把利用代码保存在数据库或文件中，当Web程序读取利用代码并输出在页面上时执行利用代码。比反射型容易利用，较为隐蔽且不用考虑绕过浏览器过滤。挖掘时也是需要寻找未过滤的输入点和未过滤的输出函数（可能完全不在同一个业务流中），可以根据当前代码功能去猜，或追寻数据有在哪里被操作，使用表名、字段名去代码里搜索。

##### 漏洞防范
1. 特殊字符HTML实体转码。
1. 标签事件属性黑白名单。

#### CSRF漏洞

##### 挖掘经验
主要用于越权操作，所以漏洞会出现在有权限控制的地方。黑盒挖洞可以先搭建环境，打开几个有非静态操作的页面，抓包看看有没有token，没有token就不带referer直接请求该页面，返回数据一样的话，可能存在CSRF漏洞。白盒审计，通读代码时看看被大量引用的基础文件（核心文件）、你比较关心的功能点代码内有没有验证token和referer相关的代码，或者直接搜索token关键字。

##### 漏洞防范
1. 增加token/referer验证避免img标签请求的水坑攻击。
2. 增加验证码。（比较麻烦，更适用于敏感操作页面）

### 漏洞挖掘与防范（进阶篇）

#### 文件操作漏洞

##### 文件包含漏洞
文件包涵函数有include()、include_once()（前两个在包含文件时即使遇到错误，下面的代码仍然执行）、require()、require_once()（这两个在包含文件时遇到错误会报错退出程序）。

###### 挖掘经验
文件包含漏洞大多出现在模块加载、模板加载以及cache调用的地方。在挖掘漏洞时可以跟踪程序运行流程，看模块加载包含的文件是否可控等，另一个是直接搜索上文四个函数来回溯寻找可控变量。一般该类漏洞都是本地文件包含，大多需要截断。

###### 本地文件包含
本地文件包含（local file include，LFI），大多出现在模块加载、模板加载和cache调用，有多种利用方式，如上传一个允许上传的文件格式的文件再包含以执行代码，包含PHP上传的临时文件，在请求URL或ua里面加入要执行的代码，WebServer记录到日志后再包含WebServer的日志，还有像Linux下可以包含/proc/self/environ文件。

###### 远程文件包含
远程文件包含（remote file include, RFI），需要设置allow_url_include = On，相比于本地包含来说更容易利用，但出现频率不高。

###### 文件包含截断
1. 使用`%00`截断，最古老的方法，受限于GPC和addslashes等函数的过滤，另外PHP5.3之后的版本已经全面修复，不能使用该方法了。
1. 使用多个英文句号`.`和反斜杠`/`来阶段，不受GPC限制，但同样在PHP5.3之后被修复。
1. 远程文件包含时利用问号`？`来伪截断，不受GPC和PHP版本限制，只要能返回代码给包含函数就能执行。在HTTP协议里，访问http://remotehost/i.txt和访问http://remotehost/i.txt?.php返回的结果是一样的，因为WebServer把问号之后的内容当成请求参数，而txt不在WebServer里解析，参数对访问i.txt返回的内容不影响，实现伪截断。
