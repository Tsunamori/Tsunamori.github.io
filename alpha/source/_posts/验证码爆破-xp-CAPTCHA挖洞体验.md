---
title: 验证码爆破-xp_CAPTCHA挖洞体验
toc: true
date: 2023-05-15 15:47:41
tags: [WP]
categories: [学习笔记]
---

最近在挖生产环境的漏洞，试用了一下xp_CAPTCHA的白嫖版和付费API版，体验其实都挺好的，样本太少就不对比识别准确性了。主要写写安装和使用步骤以及踩过的坑和相应处理方式。

# 安装踩坑（xp_CAPTCHA白嫖版）

我是java 11.0.13以及python 3.10.7, jar包直接装的java8编译版本无报错通过，但server.py（release里面有）依赖的`onnxruntime`不支持3.10，所以这里没有办法，下载了作者的打包版，在win机器使用虚拟py 3.6启动（这里也给后面带来了一个小坑，会解释的），作者的打包版见[项目readme](https://github.com/smxiazi/NEW_xp_CAPTCHA)下方网盘链接。

下载下来之后解压双击.bat文件就可以启动了，任何需要修改的，进Python36文件夹编辑server.py。

因为我尝试爆破的也是一个POST请求，所以主要的操作方式是参照这个[链接](https://mp.weixin.qq.com/s/iA9XEuoTyjOAxSDoIJsm5Q)。

这里踩了一些坑：

1. 80端口被系统占用：解决方法参考[链接](https://blog.csdn.net/weixin_41377835/article/details/112302893),(这里也可以选择改成别的端口)
2. Python requests库报错SSLError: dh key to small()：解决方法参考[链接](https://blog.csdn.net/weixin_47383889/article/details/125019751)，因为我使用的是作者打包的py3.6，所以采用第二种方法，加入语句`requests.packages.urllib3.util.ssl_.DEFAULT_CIPHERS = 'DEFAULT:@SECLEVEL=1'`降级。

所以我最终使用的test脚本是这样的：

```
#coding:utf-8
from flask import Flask
import requests
requests.packages.urllib3.util.ssl_.DEFAULT_CIPHERS = 'DEFAULT:@SECLEVEL=1'

app = Flask(__name__)

@app.route("/")
def get_captcha():
  burp0_url = "https://xxx/getVerificationCode"
  burp0_headers = {"Cookie":"xxxxx","Sec-Ch-Ua": "\" Not A;Brand\";v=\"99\", \"Chromium\";v=\"99\", \"Google Chrome\";v=\"99\"", "Accept": "application/json, text/plain, */*", "Sec-Ch-Ua-Mobile": "?0", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36", "Sec-Ch-Ua-Platform": "\"Windows\"","Sec-Fetch-Site": "cross-site", "Sec-Fetch-Mode": "cors", "Sec-Fetch-Dest": "empty", "Accept-Encoding": "gzip, deflate", "Accept-Language": "zh-CN,zh;q=0.9", "Connection": "close"}
  requ = requests.post(burp0_url, headers=burp0_headers, verify = False)
  print(requ.text)
  return requ.text

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=6666, debug=True)
```

# 使用踩坑

这里踩坑主要是对抗资产本身的一些校验机制，譬如我遇到的是请求验证码的Cookie（放在test.py的header里）要和请求登录的包内cookie保持一致，不然可以识别到验证码但请求登录时还是提示验证码错误。

高级用法同样参考上述[这个链接](https://mp.weixin.qq.com/s/iA9XEuoTyjOAxSDoIJsm5Q)和作者的readme实例。