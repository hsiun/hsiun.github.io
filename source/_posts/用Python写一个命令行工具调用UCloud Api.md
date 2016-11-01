---
title:  用Python写一个命令行工具调用UCloud Api
date: 2016-11-1 14:18
tags: 个人项目
---

### 想法来源
之前一直想通过UCloud的Api做点事情，但又没有太好的想法，直到我遇到阿里命令行工具的时候豁然开朗，我或许可以写一个这样的工具啊，然后在发布到pypi上，多么美妙的一件事，说写就写起来，先查好资料。

[如何发布一个Python命令行工具](http://www.jianshu.com/p/eb27d5cb5e1d)
[命令行工具 CLI_用户指南](https://help.aliyun.com/document_detail/43039.html?spm=5176.doc30001.2.1.mYdznw)
[使用 Python 创建 UNIX 命令行工具](https://www.ibm.com/developerworks/cn/aix/library/au-pythocli/)
[aliyun-cli](https://github.com/aliyun/aliyun-cli/blob/master/aliyuncli/aliyunCliHelp.py)
[argparse - 命令行选项与参数解析（译）](http://blog.xiayf.cn/2013/03/30/argparse/)
[Python中的命令行解析工具介绍](http://lingxiankong.github.io/blog/2014/01/14/command-line-parser/)
[Python Egg](http://www.xiaoh.me/2015/12/11/python-egg/)

### u-cli概述

我打算编写的这个工具名字就叫u-cli吧，之前打算叫ucli的但是这个名字在pypi中已经被占用了，于是只能使用这个名字了。现在已经做好一个叫uclime的。这个名字的灵感来源是sublime。感觉挺不错。

### u-cli使用的技术
u-cli使用了argparse来做命令行参数解析，使用了ConfigParser 来做配置文件的解析。后端通过UCloud SDK来调用UCloud API。

### 使用方法
使用可以直接通过下面命令安装。
```
pip install u-cli
```
执行配置，输入用户公私钥。
```
u-cli config
```

执行产品命令
```
u-cli uhost DesciberImage limit=4
```

### 项目地址

<https://github.com/hsiun/ucli>