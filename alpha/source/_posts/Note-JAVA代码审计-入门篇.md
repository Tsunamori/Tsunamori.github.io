---
title: note_JAVA代码审计-入门篇
toc: true
date: 2021-09-22 09:47:07
tags:
categories: [100 Cyber security, 140 Code review 代码审计, 149 Notes]
---

## 第1章 初识JAVA代码审计

## 第2章 代码审计环境搭建
1. 使用环境：JAVA JDK、Docker、Vulhub、IntelliJ IDEA(远程调试)、VMware、Maven

## 第3章 代码审计辅助工具简介
1. 代码编辑器：Sublime、IDEA、Eclipse
1. 测试工具：Burp Suite、SwitchyOmega、Max HackerBar、Postman、Postwoman、Tamper Data、Ysoserial、Marshalsec、MySQL监视工具、Beyond Compare
1. 反编译工具：JD-GUI、FernFLower、CFR、IntelliJ IDEA
1. JAVA代码静态扫描工具：Fortify、VCG、FindBugs与FindSecBugs插件、SpotBugs
1. 公开漏洞查找平台：CVE、NVD、CNVD、CNNVD

## Java EE基础知识

### Java EE

#### Java EE常用核心技术
1. Java 数据库连接 （JDBC）：用于规范客户端程序如何访问数据库的应用程序接口。
1. Java 命名和目录接口（JNDI）：Java的一个目录服务应用程序界面（API），将服务名称和对象关联起来，从而使开发可以用名称来访问对象。
1. 企业级JavaBean（EJB）：用来构筑企业级应用的、在服务端可被管理的组件。
1. 远程方法调用（RMI）：是Java的一组用户开发分布式应用程序的API，增强Java开发分布式应用的能力。
1. Servlet：是使用Java编写的服务端程序，狭义的Servlet指Java语言实现的一个接口，广义上指任何实现该Servlet接口的类。主要功能在于交互式地浏览和修改数据，生成动态内容。
1. JSP（JavaServer Pages）：一种动态网页技术标准，部署在网络服务器上，可以相应用户端发送的请求，并根据请求内容动态生成HTML、XML或其他格式文档的Web网页，返回给请求者。
1. 可扩展标记语言（XML）：是被设计用于传输和存储数据的语言。
1. Java消息服务（Java Message Service, JMS）是一个Java平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间或分布式系统中发生消息，进行异步通信。

#### Java EE 分层模型
1. Domain Object(领域对象)层：由一系列POJO（Plain Old Java Object）组成，这些对象是该系统的Domain Object，通常包含各自所需实现的业务逻辑方法。
1. DAO(Data Access Object, 数据访问对象)层：由一系列DAO组件组成，实现对数据库创建、查询、更新、删除等操作。
1. Service(业务逻辑)层：由一系列控制器组成，用于拦截用户请求，并调用业务逻辑组件的业务逻辑方法处理用户请求，根据处理结果向不同View组件转发。
1. View(表现)层：由一系列页面及视图组件组成，负责收集用户请求，并显示处理后的结果。

### MVC

MVC，即为Model View Controller，三个核心部件，独自处理各自的任务。这种分离思想应用在代码审计中，以抓住关键问题。

#### Java MVC
1. Model：表示携带数据的对象或Java POJO，即使模型内的数据改变，它也具有逻辑来更新控制器。
1. Controller：表示逻辑控制，它对模型和视图都有作用，控制数据流进入模型对象，并在数据更改时更新视图，是视图和模型的中间层。
1. View：表示模型包含的数据的可视化层。
工作流程：控制层接收用户请求，决定调用哪个模型，模型处理用户请求并返回数据，最后由视图层将数据转化呈现给用户。MVC模式使视图层和业务层分离，比如修改View层代码时，不用重新编译Model和Controller代码。

#### Java MVC框架
1. Struts1
1. Struts2
1. Spring MVC
1. JSF
1. Tapestry

### Servlet
