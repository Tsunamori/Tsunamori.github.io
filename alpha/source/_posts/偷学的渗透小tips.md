---
title: 偷学的渗透小tips
toc: true
date: 2021-12-05 19:40:18
tags: Summary
categories: [100 Cyber security, 110 Bug bounty, 112 Ideas]
---

(人菜就要多偷学)

# 简单套路

1. 当请求为`数据更新操作（添加、删除、修改等）`，且请求包没添加（或可绕过）Referer来源地址、随机CSRF-token时，考虑CSRF/XSRF攻击（burp自动生成payload）
1. 上传位置可以尝试上传html，验证有无html解析（毕竟万年碰不到一个能传马的点了！），危害为钓鱼、挂马、挂黑页等。
    ```
<html>
    <body>
          <script>alert(1)</script>
    </body>
</html>
    ```
1. 测越权，burp插件携带权限cookie批量请求值得拥有（实现该功能的插件很多，比如Autorize）。
1. JS经常泄漏各种API接口（找链接： https://github.com/Threezh1/JSFinder ，也有非链接需要自己构造包含函数名称及参数的数据包的JS，被动搜索浏览器插件： https://github.com/momosecurity/FindSomething ）。
1. 链接里带有URL的点可以试试改成任意URL测重定向（大概算是依靠链接前半部分造成链接可信任，结合短链接等伪装手法，重定向钓鱼）。

# 骚操作

## APP
1. ***逆向分析YYDS！***
1. APP脱壳找证书，放进抓包工具反编译流量（适用于流量内容不可见的情况，具体教程找找看）。
1. `adb logcat >> x.txt`输出本地日志，可能包含本机用户使用APP时输入的敏感信息。
1. 快速并发数据包，可以实现批量发送短信、刷赞等。（burp-turbo）
1. 将拦截的`响应包`内容从fail改为success可以实现验证码绕过等（是原理清楚但从来没改过响应包的我），证明校验为前端校验。
1. Activity劫持（涉及安卓端命令调用了，目测是比较冷门可挖的）。
1. LaunchAnyWhere （https://chan-shaw.github.io/2020/04/12/LaunchAnyWhere%E7%BB%95%E8%BF%87%E5%8E%9F%E7%90%86/）
1. (这是一条防御办法)当APP做证书绑定后，可以在被抓包时不发送业务请求（但据说有办法仍然实现抓包，目前未知。）。

## 小程序
1. 发送请求参数被加密时，可解包找公钥。（这怕是不太能防？还是说果然RSA/RSA2不靠谱？）
1. 绕过批量发送限制：在参数末尾每次多加一个对整体无作用的`+`（猜测只有当参数在请求末端可行？）。
1. 测批量时，不仅可以考虑本网站用户的手机号被批量的问题，也可以加上非注册用户手机号实现批量轰炸的情况（扩大漏洞影响）。

## Web
1. api接口无法直接访问时（如swagger），把脚本配置文件（如dirsearch）的host改成网站IP。
1. 注意邮件、短信等携带的链接、短链接内容，可能存在参数可枚举的问题。
1. 必应、雅虎、搜狗、谷歌、etc，没准就有一个可以搜到奇怪的带权限链接。
1. 地址接口
    ```
/fonts/
/pdf/
/js/
/css/
/img/
    ```
1. 浏览器开插件模拟手机端。
1. 在线客服处总容易出现奇怪的越权/敏感信息泄漏。
1. 抓包改发送的邮件内容搞钓鱼（有意思的思路）。
1. HTTP请求走私（看来还是该多打CTF）
1. ip/a/b 会强制跳转到登录口，但ip/a/c不会，则使用ip/a/c/../b绕过跳转。访问xx.jsp会强制转到访问aa.jsp，访问aa.jsp/../../(../多个)/xx.jsp实现绕过过滤。
1. 查询参数留空，可能会出现一次性相应所有查询内容的问题。（遇到过几次，和同事讨论过，大概是因为留空自动从where里排除了，导致全量查询。算是平时比较难去想到和测试的点。）
1. LDAP 未授权访问 （相比于危害来说，利用未免太过轻松。。。）

## IOT
1. MQTT协议越权/未授权

# 记录一下看到过的burp插件

1. JSON Decoder 解码JSON（新burp不是可以直接美化json？）
1. MarkINFO (https://github.com/UUUUnotfound/BurpSuite-Extender-MarkInfo) 高亮敏感信息
1. highlighter-and-extractor
1. APIKit (https://github.com/API-Security/APIKit)
1. BurpCrypto
1. BurpDomain (https://github.com/404SEC/BurpDomain)
1. turbo
1. Logger++
1. Bypass WAF
1. JSON Web Tokens
1. lazyCSRF https://github.com/tkmru/lazyCSRF/releases/
1. ActiveScan++
1. LinkFinder

# 记录一下其他脚本工具
1. reverse-sourcemap 还原jsmap （可以用curl命令对付不能直接下载的js.map文件）
1. wxappUnpacker
1. 微信小程序加解密脚本：http://82.156.16.24/index.php/2021/05/26/4.html
