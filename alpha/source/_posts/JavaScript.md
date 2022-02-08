---
title: JavaScript-原型链污染
toc: true
date: 2021-04-22 14:33:21
tags: Summary
categories: [100 Cyber security, 120 CTF, 121 Web ]
---

## JavaScript原型链

JavaScript是动态的，本身不提供一个 `class` 的实现。即便是在 ES2015/ES6 中引入了 `class` 关键字，但那也只是语法糖，JavaScript 仍然是基于原型的。

当谈到继承时，JavaScript只有一种结构：对象。每个实例对象（object）都有一个私有属性（称之为 __proto__ ）指向它的构造函数的原型对象（prototype）。该原型对象也有一个自己的原型对象（__proto__），层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。

## 原型链攻击

实质上是通过解析JSON的方式，将`__proto__`作为键名而非原型，赋值给对象，从而污染原型链。
通常利用`对象merge`或`对象clone（其实内核就是将待操作的对象merge到一个空对象中）`的方式控制并操作键名。

## 参考材料

1. 深入理解JavaScript Prototype 污染攻击 https://www.leavesongs.com/PENETRATION/javascript-prototype-pollution-attack.html
1. 浅析javascript原型链污染攻击 https://xz.aliyun.com/t/7182
1. 再探 JavaScript 原型链污染到 RCE https://xz.aliyun.com/t/7025

知识点解说和题解还是得先看1，p神太帅了。3是一个RCE拓展，（就是预判太跳跃了，大佬太快我跟不上QAQ）。

关于p神举的js污染样例，我自己又写了一下，实际上得到的是这样的：
```
>> let o2 = {a:1, __proto__:{b:2}}
< undefined
>> o2
< {…}
	a: 1
	<prototype>: {…}
​​		b: 2
​​		<prototype>: Object { … }
```

也就是说，o2的__proto__实际上并没能赋上值，而是赋值给了o2的prototype，所以导致o3被赋值null后无法继承b。
利用JSON赋值后，得到：
```
>> let a2 =  JSON.parse('{"a":1, "__proto__":{"b":2}}')
< undefined
>> a2
< {…}
	__proto__: Object { b: 2 }
	a: 1
	<prototype>: Object { … }


```
这个时候a2的__proto__就已经赋上值，改变了指针指向。