---
title: 在kali上安装clash/V2raya的教程
toc: true
date: 2022-09-23 02:33:04
tags:
categories: [600 随便叨叨]
---

19年写过的东西了，当时被小管家gank文章无了，好在还有备份。这次更新V2raya的时候正好放上来，给自己一个安装指导。

然后，本文仅提供Clash和V2raya的Kali系统对比和安装教程（因为Win的要简单多了）。

先把结论放在最前面，Clash的优点是可以自动切换IP，v2raya要手动切，但个人使用之后感觉Linux端Clash真心不如V2raya，不过win端Clash的舒适度大于V2raya。我自己经过反复踩雷之后选择Kali虚拟机用V2raya，主机Windows用Clash。

当然，Clash的win端想要开全局代理把运行Kali的虚拟机流量代理起来又是另一个很头秃的问题，毕竟Clash创造出来的本意似乎并不是服务于更好的全局代，我至今没能找到简单又完美的开局办法（也可能因为我妥协了换成V2raya for Linux）。

V2ray for win可以直接全局代理搞定虚拟机，但是配置要比Clash麻烦一些。而且出于各种需求，我还是倾向于给Kali自己开代理，主机端就不需要一直开着代理用了。

但从安装在Kali的体验来说，V2raya更适合像我一样，经常从github下个脚本跑的人，毕竟Clash的全局代额外使用了脚本之后在kali控制台内体验仍然不好（再次羡慕Ubuntu的网络全局配置），使用Clash的话往往需要加一条 --proxy x.x.x.x:xxx，还容易不好使。Kali虽然自带proxychains，但一个是配置起来麻烦，另一个是如果用的机场，可能需要多次的手动调整配置文件。

先写Clash，V2raya在下面。

Clash：

下载合适的版本，我在kali2021上用的是clash-linux-amd64-v1.6.0.gz。

解压`gzip -d clash-linux-amd64-v1.6.0.gz `

(注意修改版本号，改成你自己下载的版本)
给个权限
`sudo chmod +x ./clash-linux-amd64-v1.6.0`
，这里想把文件名简化一下也可以。（再次注意clash的文件名可能需要改动成你自己拥有的）

运行
`./clash-linux-amd64-v1.6.0`
， 注意Clash默认每次开机后需要手动开启，也就是该命令每次开机你都需要跑一下。

然后路径
`/home/用户名/.config/clash`
下会有自动生成的配置文件，没有.config文件的去view里打开show hidden file。（用户名是你自己设置的啊，会不一样）

这里的两个配置文件要更换，如果你的XXX服务商没有提供配置文件，建议win下装个Clash，配好了再把配置文件拿过来，反正win端Clash也挺好用的，装个不亏。

config.yaml：从win端路径C:\Users\用户名\.config\clash\profiles拿到一个多半是`数字.yml`格式的yaml文件，改名成config.yaml，丢进/home/kali/.config/clash替换原文件。（注意这个yml和yaml的区别）

Country.mmdb：从win端路径C:\Users\用户名\.config\clash下拿到Country.mmdb，丢进/home/kali/.config/clash替换原文件。（Users在中文版win里面是`用户`，用户名就是你的账户名字啦）

重新运行
./clash-linux-amd64-v1.6.0
（不需要sudo），打开浏览器访问
http://clash.razord.top/#/proxies
看Clash配置，浏览器端口设置`HTTP:127.0.0.1:7890, SSL:127.0.0.1:7890, SOCKS_v5:127.0.0.1:7891`(preference->network settings,新版本似乎已经统一到127.0.0.1:7890了,socks_v5要改成7890，具体要看运行clash时候控制台显示什么)，访问外网确定能使。(运行clash的terminal不要关，关了就结束运行了)

那么到这里，问题来了，目前的配置能让你的Kali浏览器访问外网，Clash的作用仅仅是帮你在127.0.0.1:7890开了一个代理通道，但除了浏览器以外的流量想要指向代理就得自己设置。这可就有名堂了，比如控制台、环境变量、apt源、npm源、docker，这些都要自行配置指向代理通道。

如果是造轮子达人可以写个自定义脚本，我等渣渣还是优先找别人的轮子了，指路himanshub16/ProxyMan，一键配置（运行不需要sudo），这个脚本有一处难点，就是它原本是写给linux用，所以kali有些环境变量路径不适配，需要切换至bash让脚本自行适配环境变量再切回来，具体操作为:
控制台
exec bash
进入bash模式，
source ~/.bashrc
更改bashrc配置，安装ProxyMan脚本
./install
，配置生效后
exec zsh
回到zsh模式，
proxyman set
设置一键配置端口。 （这里有不会用的实在解决不了的再来问我吧，容易遇到的问题种类比较多，具体问题具体分析）

但以上仅能保证脚本内提及的各种控制台、apt源、npm源等一键配置代理，其它你会用到的临时项目都要手动指向代理通道，或者自己手动添加进配置文件or脚本。（或许也有更好的解决办法但是我还是选择v2raya）

V2raya：

Linux版建议使用v2rayA/v2rayA，比直接上原生V2ray好使。

安装方法指路官方文档，照着搞。

v2raya默认开机自启动，省心省力。(注意！现在的1.5.x版本已经不支持默认开机自启动，也不支持软件源更新时自动更新版本了，具体命令去[这里](https://github.com/v2rayA/v2rayA/issues/237)看)

访问`http://localhost:2017/`，导入你的XXX服务商生成的配置文件，记得把全局透明代理打开。

设置不会改的就保持默认，我自己开了DNS防劫持(有的时候会出现一些连不上的情况，如果排除是订阅源的问题，我会重新开关一下DNS防劫持)。


总结：写的很稀碎并不保姆，累了（划掉）

之所以大费周张地写了很多Clash内容，只是因为相对来说Clash for Kali的教程还是很少，官方文档也是云里雾里，而且也基本没人提新版本的Kali网络配置没有了全局代理，要么能打开谷歌就算胜利，要么旧版Kali教程来回搬运。偶尔有提到Clash for Linux的，也是打开配置文件挨个手动更改配置代理，这很不cool。

虽然我仍然推荐V2rayA，但或许会有人有一天用的上这些内容。

