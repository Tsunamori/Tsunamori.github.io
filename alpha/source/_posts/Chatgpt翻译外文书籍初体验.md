---
title: Chatgpt翻译外文书籍初体验
toc: true
date: 2023-03-27 18:12:17
tags: [Chatgpt]
categories: [学习笔记]
---

很喜欢的番剧5月马上要出新剧情，为了方便挖掘细枝末节推理剧情走向，我从Kobo买了之前第三季的小说。实际上这是个原创番，先有动画版再有小说版。但据考究党所言，小说里有一些动画删减的内容和补充，所以还是兴冲冲买了下来。当然我并看不懂日文，冷门番也不好找新小说的译文。于是，尝试了一下使用Chatgpt来翻译小说，算是尝试着积累一些对新玩意的体验。

使用的是python3 开源项目[bilingual_book_maker](https://github.com/yihong0618/bilingual_book_maker)，感谢作者。

当前（23.3.27）支持epub和TXT较好，pdf支持稍差。支持多种翻译api，包括Chatgpt api的gpt3 和gpt3.5（默认）、Deepl、彩云小译。暂不支持非API版本的Chatgpt free或者Chatgpt plus，易风控。

## 如何使用

我使用了Chatgpt api免费版，主要是还有$18余额没有过期。

首先挂好代理，[登录chatgpt api平台](https://platform.openai.com/account/api-keys)获取你的api key，注意这个key跟github一样，生成后只给你展示一次，如果忘了就要新建一个key。

clone项目，pip安装依赖。

具体使用命令参考项目说明，我这里用的是`python3 make_book.py --book_name test_books/xxx.epub --openai_key [你刚才申请的key]` ，默认翻译为简中所以可以不用配置。

## 使用体验

### 实际成本

总共3本日文小说，目前已经完整的翻译了一本，原文187K字，费时5.5个小时，用了$1.1，共3652个请求。（如下图）

![image.png](https://tsunamori.github.io/img/image_1679910592101_0.png)

从成本上来说，还是十分划算的。

### 翻译效果

简单截开头一页示范一下效果。（首先声明这是我自己为了方便阅读操作的，购买正版小说，仅自用，不会传播）

![image.png](https://tsunamori.github.io/img/image_1679910972280_0.png)

翻译的时候也可以选择不输出原文，但我认为原文一定有翻译无法明确表达的感觉，所以保留了原文。

可以看到内容是通顺的。当然实际还是会发现一些问题。比如一系（的）执行官被翻译成了一名执行官。但好在我对这部作品整体设定比较熟悉，阅读时可以接受一些失误。

同样，因为脚本是按照段落，一段一段的去发送翻译请求。所以对于这种几个字就分段的日式小说风格来说，请求量还是相对较多的，也会对耗时产生影响。

## 可能的优化项

在翻阅大家提的issues时，也发现了一些当前chatgpt翻译的问题，比如说上下文之间人名对不上（在日文小说里倒是还好，英文小说可能更严重一些），据说修改prompt可以优化。

现在单线程翻译很慢，脚本pr已经支持了多线程配置，但目前开发者测试下来还相当不稳定（主要是api暂时不那么支持多线程），所以并不推荐。

如果经常使用且自己有翻译功底可以比较清晰的明白AI译文的失误在哪里的话，应该可以调整prompt来优化译文。我自己并不常用所以暂时还是使用默认配置的prompt了。

## Reference

[bilingual_book_maker](https://github.com/yihong0618/bilingual_book_maker)
[Kobo电子书转换epub](https://www.bilibili.com/video/BV1Ny4y1b7cH/)
[adobe digital editions 4.5.11 installers](https://www.adobe.com/solutions/ebook/digital-editions/download.html)
[ebook conventer](https://www.ebook-converter.com/download/installebookconverter.htm)