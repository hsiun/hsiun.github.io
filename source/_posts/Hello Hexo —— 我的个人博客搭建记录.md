---
title: Hello Hexo —— 我的个人博客搭建记录
---
[Hexo](http://hexo.io/)是一个Node编写的，可以根据Markdown语法生成静态文件的博客系统，这篇博客是根据网上教程，使用Hexo + Github搭建个人博客过程的记录。Hexo搭建过程非常简单，官方文档写的非常清楚。这篇 博客的写作原因仅仅在于记录自己的安装过程。作为一个记录。

### 前提条件

Hexo的安装过程依赖Node.js和Git，所以安装Hexo前需要先安装着两个软件。他们的下载地址可以参考下面的链接。

[Node.js](https://nodejs.org)
[Git](git-scm.com)

安装过程非常简单，也就是下载两个软件包安装就可以了。其中node.js的安装，可以先安装nvm，通过nvm来安装node.js。

### Hexo的安装

hexo安装可以在命令行下完成。通过借助使用git bash来执行下列命令，git bash的启动在任意一个目录中右键，找到git bash点击就可运行。这个方法在使用github管理代码的时候，生成本地证书的时候还会用到。

``` bash
$ npm install -g hexo-cli
```

### 使用Hexo建站

首先创建好需要存放站点的目录，在这里是<folder>。通过下面命令可以在<folder>目录下生成建站所需的文件

``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```

### 使用git管理代码

修改<folder>目录下的_config.yml文件，添加下面行，表示使用git来管理代码，并把代码托管在github上。当然首先需要您在github上创建yourname.github.io项目。这里的托管方式借鉴网上一些方法，使用master分支来管理静态网站。code分支来管理生成静态文件的代码。

```
deploy:
  type: git
  repo: git@github.com:yourname/yourname.github.io.git
  branch: master
```

### 把代码部署到github

运行下面命令就可以将代码部署到github了。

``` bash
$ hexo generate
$ hexo deploy
```
### 访问yourname.github.io观察效果
