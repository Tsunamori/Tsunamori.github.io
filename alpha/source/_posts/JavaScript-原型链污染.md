---
title: JavaScript-原型链污染
toc: true
date: 2022-02-8 17:58:21
tags: Summary
categories: [100 Cyber security, 120 CTF, 121 Web ]
---

# 简单的背景介绍

## JavaScript原型链

JavaScript是动态的，本身不提供一个 `class` 的实现。即便是在 ES2015/ES6 中引入了 `class` 关键字，但那也只是语法糖，JavaScript 仍然是基于原型的。

当谈到继承时，JavaScript只有一种结构：对象。每个实例对象（object）都有一个私有属性（称之为 __proto__ ）指向它的构造函数的原型对象（prototype）。该原型对象也有一个自己的原型对象（__proto__），层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。

## 原型链攻击

实质上是通过解析JSON的方式，将`__proto__`作为键名而非原型，赋值给对象，从而污染原型链。
通常利用`对象merge`或`对象clone（其实内核就是将待操作的对象merge到一个空对象中）`的方式控制并操作键名。

# 学习材料

1. 深入理解JavaScript Prototype 污染攻击 https://www.leavesongs.com/PENETRATION/javascript-prototype-pollution-attack.html
1. 浅析javascript原型链污染攻击 https://xz.aliyun.com/t/7182
1. 再探 JavaScript 原型链污染到 RCE https://xz.aliyun.com/t/7025
1. [Node] child_process.fork 与 env 污染 RCE https://blog.mmf.moe/post/node-fork-proto-env-rce/
1. 从Kibana-RCE对nodejs子进程创建的思考 https://xz.aliyun.com/t/6755

知识点解说和题解还是得先看1，p神太帅了。3是一个RCE拓展，（就是预判太跳跃了，大佬太快我跟不上QAQ）。

关于p神举的原型链污染样例，我自己又写了一下，实际上得到的是这样的：
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

第4篇是从node.js漏洞角度来分析，在我看来侧重点还是有些区别，更像进阶内容。

# 实战分析

## 2020-第五空间-hard_node

这个时候再翻回来看那道题。
源码如下：
```
// app.js
const express = require('express');
const bodyParser = require('body-parser');
const proc = require('child_process');
const request = require('request');
const ip = require("ip");
const manage = require("./manage.js");
const path = require('path');

const app = express();
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

app.use(express.static(path.join(__dirname, 'public')));

//stop hackers
const disallowedKeys = [
    '__proto__',
    'prototype',
    'constructor',
    'eval','proccess','root','global','exec','!','fs'
];

function isValidPath(segment){
    disallowedKeys.forEach(evilWord => {
        if(segment.toString().indexOf(evilWord)!==-1){
            return false
        }
    });

    return true;
}

app.post('/add', (req, res) => {
    let ip = req.ip;
    console.log(ip.m);
    if (ip.substr(0, 7) == "::ffff:") {
        ip = ip.substr(7)
    }
    console.log(`method:${req.method},serverip:${server_ip},ip:${ip}`);

    if (ip != '127.0.0.1' && ip != server_ip) {
        res.status(403).send('Not Edit from Local!');
    }else{
        if(req.body.userName && req.body.nameVal){
            let username = req.body.userName;
            let nameVal = req.body.nameVal;

            if (!isValidPath(username) || !isValidPath(nameVal)) {
                username = 'username';
                nameVal = 'guest';
            }

            manage.set(object, username, nameVal);
            console.log(ip.k);
            console.log(object);

            res.send(`
            <h1>Edit Success</h1>
            <a href="/admin">View Admin Page</a>`)
        }else{
            res.send('param error');
        }
    }
});

app.get('/admin',(req,res)=>{
    if(manage.get(object,'username','guest') === 'admin'){
        console.log('Current User:'+object.username)

        const child = proc.fork(`${__dirname}/public/user.js`,['admin']);
        child.on('message', (body) => {
            res.status(200).send(body);
        });
        child.on('close', (code, signal) => {
            console.log(`subproccess ended with ${signal}`);
        });

    }else{
        res.status(403).send('Only Admin Can View this');
    }
})

app.get('/getContent',(req,res)=>{
    res.sendfile(`${__dirname}/public/guest.html`);
})

app.get('/', (req,res) => {
    // console.log(req.body)
    let uri = req.query.url? req.query.url: 'http://127.0.0.1:3000/getContent';
    console.log(uri)

    try{
        request.get(uri,(err,response,data)=>{
            if (!err && response.statusCode == 200) {
                res.send(data);
            }else{
                console.log(err);
            }
        })
    }catch(e){
        console.log(e);
    }finally{
        console.log('Make Server Continue Running');
    }
});

var object = {username:'guest'};
var server_ip = ip.address();

app.listen(3002);
console.log(`${server_ip} is starting at port 3000`)
```

```
// manage.js
const isObj = require('is-obj');
var manage = {
    getPathSegments: function(path) {
        const pathArray = path.split('.');
        const parts = [];
        for (let i = 0; i < pathArray.length; i++) {
            let p = pathArray[i];
            while (p[p.length - 1] === '\\' && pathArray[i + 1] !== undefined) {
                p = p.slice(0, -1);
                p += pathArray[++i];
            }
            parts.push(p);
        }
        return parts;
    },
    get: function(object, path, value) {
        if (!isObj(object) || typeof path !== 'string') {
            return value === undefined ? object : value;
        }
        const pathArray = this.getPathSegments(path);
        for (let i = 0; i < pathArray.length; i++) {
            if (!Object.prototype.propertyIsEnumerable.call(object, pathArray[i])) {
                return value;
            }
            object = object[pathArray[i]];
            if (object === undefined || object === null) {
                if (i !== pathArray.length - 1) {
                    return value;
                }
                break;
            }
        }
        return object;
    },
    set: function(object, path, value) {
        Object.keys(Object.prototype).forEach(function(Val){
            if(!Object.hasOwnProperty(Val)){
                delete Object.prototype[Val];
                console.log(`${Val} is delete`);
            }
        })
        if (!isObj(object) || typeof path !== 'string') {
            return object;
        }
        const root = object;
        const pathArray = this.getPathSegments(path);
        for (let i = 0; i < pathArray.length; i++) {
            const p = pathArray[i];
            if (!isObj(object[p])) {
                object[p] = {};
            }
            if (i === pathArray.length - 1) {
                object[p] = value;
            }
            object = object[p];
        }
        return root;
    }    
}
module.exports = manage 
```