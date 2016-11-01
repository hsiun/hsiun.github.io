---
title:  Gitbook写书
date: 2016-11-1 13:35
tags: 个人项目
---

### Gitbook简介

这两年Markdown和Github可以说非常的火，Gitbook是一个基于这两种技术编写开源书籍的平台，这段时间也可以说是非常的火，在Gitbook下写书，你只需要编写一个summary文件，这个summary文件相当于是目录。Gitbook会自动根据这个summary文件生成对应的md文件，然后再填充相应的文件就可以了。Gitbook提供了Web页面也提供了响应的客户端。但是页面在国内访问速度不理想，建议还是用客户端，客户端是nodejs开发的因此需要nodejs环境。


### Gitbook使用

首先是先需要注册gitbook的账号，可以在gitbook官网注册，注册的时候可以使用github账号注册，使用github账号注册的好处在于后面使用github来管理源文件比较方便。然后设置好关联，我的用法是在gitbook上创建好书籍，然后在github上创建对应的项目，再将二者管理起来。然后从github上把相关项目clone一份到本地，然后在本地编辑这个repository。这样每次把写好的内容更新到github上，gitbook上就能获取到更新，然后可以通过gitbook来浏览相应的书籍了。


### 设置自定义域名

gitbook支持自定义域名设置，具体的设置方法可以查询他的官网文档。大概的做法就是先在gitbook网站上写上要解析的域名，这个之前他是分内容和封面可以设置不同的域名的，现在不做区分了都是一个域名。写好之后，在在域名商那边做解析，cname解析，解析到gitbook.com这一点比较奇怪。gitbook相当于是做了一个中间代理的角色，你把域名解析到它那边，它会自动根据你做的解析来完成解析。

最后是我按照这样的过程翻译的一本书籍，可以通过（<https://rest-with-spring.gaospot.com/>）访问，内容本身放在gitbook上，访问较慢。

### 参考

[使用Gitbook写开源书籍，过一把作家瘾](http://www.jianshu.com/p/7476afdd9248)
[Markdown 编辑器语法指南](https://segmentfault.com/markdown)
