---
title: CTF MD5
toc: true
date: 2022-02-08 10:30:06
tags: CTF
categories: [100 Cyber security, 120 CTF, 121 Web ]
---

## 2020

### 第五空间-决赛 hard_node (node.js原型链污染)
这个题看了原型链污染没什么灵感（因为没找到可以推的源码），最终在某个blog上找到了源码和WP （https://blog.mmf.moe/post/node-fork-proto-env-rce/ ），但似乎不能照着拿flag，不知道是不是后续被做了改动，但这个知识点算是学会了。
另外，httpie可以使用`-v`参数来查看具体发送出去的请求内容。

### RCTF-swoole (反序列化)
CTFHub的靶场时间很短，所以还是拿到源码先线下分析。
```
#!/usr/bin/env php
<?php
Swoole\Runtime::enableCoroutine($flags = SWOOLE_HOOK_ALL);
$http = new Swoole\Http\Server("0.0.0.0", 80);
$http->on("request",
    function (Swoole\Http\Request $request, Swoole\Http\Response $response) {
        Swoole\Runtime::enableCoroutine();
        $response->header('Content-Type', 'text/plain');
        // $response->sendfile('/flag');
        if (isset($request->get['phpinfo'])) {
            // Prevent racing condition
            // ob_start();phpinfo();
            // return $response->end(ob_get_clean());
            return $response->sendfile('phpinfo.txt');
        }
        if (isset($request->get['code'])) {
            try {
                $code = $request->get['code'];
                if (!preg_match('/\x00/', $code)) {
                    $a = unserialize($code);
                    $a();
                    $a = null;
                }
            } catch (\Throwable $e) {
                var_dump($code);
                var_dump($e->getMessage());
                // do nothing
            }
            return $response->end('Done');
        }
        $response->sendfile(__FILE__);
    }
);
$http->start();
```
