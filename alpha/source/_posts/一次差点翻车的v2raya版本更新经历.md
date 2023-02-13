---
title: 一次差点翻车的kali v2raya版本更新经历
toc: true
date: 2023-02-14 00:09:05
tags: 踩坑记录
categories: [600 随便叨叨]
---

事情的起因是这样的，我在公司和家里各有一个kali虚拟机，很多新工具骚操作都是先在公司机上跑过觉得好用再回家搞一遍。
由于一些历史原因，我的kali装了v2raya做代理之后并没有跟随apt-get一起更新版本，所以跑了很久的1.5版本。后来发现2.x版本有了负载均衡加上一些其它优化，比较心动，遂更新了一遍，没想到第二遍在家做的时候险些翻车。

正常的更新路线大抵是这样的：参考[官方文档](https://v2raya.org/en/docs/prologue/installation/debian/)，由于我不打算跟随最新版本一起更新，所以使用的是下载deb安装包的方式更新。以下是正确的操作步骤：

1. 下载最新deb[安装包](https://github.com/v2rayA/v2rayA/releases) 
2. 使用官方推荐脚本更新xray-core `curl -Ls https://mirrors.v2raya.org/go.sh | sudo bash`
3. apt安装deb包

但是，因为一些失误，我这里是先更新了v2raya版本没有更新xray-core，导致新版本v2raya无法运行，报错core版本低。也就是说，我翻不了墙，除非更新core版本，但官方脚本又要翻墙运行。。。

当然，这里其实还是尝试了用物理机直接下好core包丢进虚拟机，但由于众所周知的vmtools兼容性问题，这里丢不进来，哈哈哈（以及送给大家一个我平时用着挺好但今天没能解决问题的激活vmtools的方式：控制台直接输入`usr/bin/vmware-user`）

vmtools的问题晚点再说，我又换了一个思路，如果你有看过我之前发的内容，那么你会知道我的物理机-windows是用了clash for windows（很遗憾，是没有tun模式的老版本）。那么如何让clash for windows代理我的kali虚拟机流量呢？

闲言少叙，我还要赶紧睡觉，总之，clash开启`allow LAN`选项，物理机端打开控制台获取对外网卡IP，虚拟机网络配置不确定有没有影响，我这里是保持桥接。clash如果没有更改过端口的话应该是默认7890。（当然如果你是有tun模式的clash好像是可以直接开启就行）

这样我们就拿到了proxy的地址：`物理机ip:7890`

虚拟机端配网页访问比较简单，不清楚的可以看ref第一个链接。控制台有两种方式，一种是配置bash的proxy，也是同样在ref第一个链接（我印象里这部分内容我之前搞clash for kali的时候踩过坑，需要手动配置的项目有很多，不只是配bash proxy就可以的，感兴趣的可以看另外那篇文章，因为我不是希望永久使用物理机clash来代理虚拟机流量所以我不折腾配置）

另一种配置控制台的方式就是用kali自带的proxychains4。

sudo权限编辑proxychains4配置`sudo vi /etc/proxychains4.conf `，在最后一行添加`socks5 物理机ip 7890`，保存关闭。

然后以proxychains运行我们想要的更新core脚本：`proxychains curl -Ls https://mirrors.v2raya.org/go.sh | sudo proxychains bash`，注意这里因为有个管道符，所以应该在管道符前后两个命令都加上proxychains，以及后面的命令是sudo在前proxychains在后。

于是成功利用物理机clash作为代理更新好了core版本，虚拟机又可以快乐起来了！




ref:
1. https://www.ngui.cc/article/show-586553.html?action=onClick
2. https://blog.csdn.net/weixin_68281676/article/details/125477791#:~:text=%E9%85%8D%E7%BD%AE%20%E6%96%87%E4%BB%B6%E8%B7%AF%E5%BE%84%E4%B8%BA%EF%BC%9A%2Fetc%2F%20proxychains.conf%EF%BC%8C%E5%B0%86%E6%96%87%E4%BB%B6%E6%8B%89%E5%80%92%E6%9C%80%E5%BA%95%E9%83%A8%E3%80%82%20proxychains,%E8%BF%90%E8%A1%8C%E6%96%B9%E6%B3%95%EF%BC%9A%20proxychains%20%2B%20%E7%A8%8B%E5%BA%8F%E3%80%82