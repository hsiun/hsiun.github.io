---
title: Redmine介绍
date: 2016-02-17 20:01:53
tags: 系统运维
---

每天记Redmine是我现在的工作内容之一，之前一直没有深入的了解Redmine究竟是用来干嘛的的，直到最近看到有人分享了一篇[《最佳工程实践－基于Redmine的项目管理平台》](http://www.jianshu.com/p/cd7a12fa09bb)的文章，于是对Redmine这个东西又起了一些兴趣。

> Redmine是一个基于web免费且开源的项目管理和问题追踪工具。它一些用户管理多个项目已经和他相关的子项目。它的特点包括每一个项目的wikis和论坛，时间追踪，和灵活的基于角色的访问控制。它包括日历和甘特图去对项目和他们的期限提供可视化视角。Redmine正好了多种版本控制系统并且包括了一个库浏览工具和diff查看器。——引用自Wikipedia

通过《最佳工程实践－基于Redmine的项目管理平台》这篇文章，认识了一个叫做[bitnami](https://bitnami.com/stack/redmine) 的。这上面提供了一个安装Redmine的集成环境，并且还提供了已经安装好Redmine的VMware镜像。不知道什么时候会不会提供docker镜像：）。

bitnami上提供了Redmine试用，bitnami会自动在aws上安装好Redmine，然后会提供给你一个连接，直接使用就可以了。如果想自己安装Redmine可以安装bitnami官方提供的教程。


安装过程
----

下载Redmine安装包

```bash
wget https://downloads.bitnami.com/files/stacks/redmine/3.2.0-2/bitnami-redmine-3.2.0-2-linux-x64-installer.run

chmod u+x bitnami-redmine-3.2.0-2-linux-x64-installer.run

./bitnami-redmine-3.2.0-2-linux-x64-installer.run

```

依照上面的步骤一步一步下来就可以安装Redmine了。安装完之后可以安装插件，更新数据。也可以直接访问。

访问
--

在服务器本地可以在http://127.0.0.1:80访问，如果是在公网环境下可以通过服务器的公网ip访问。