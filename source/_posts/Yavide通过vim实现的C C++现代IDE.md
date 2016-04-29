---
title: Linux学习过程中的问题
date: 2015-10-22 23:42
tags: 开发工具
---

vim有大量为了开发者的插件，并且当他们整合在一起vim就变得比除此以外任何IDE都要强大。另外的优点就是资源消费少，和Eclipse或者NetBeans比较是它是最低的。这也使得基于vim的IDE的主意适合远程编程。Yavide是一个认真使用vim插件的新项目，并且尝试把所有的事情打包到一起。它也致力于提供一些在其他IDE不可用的功能。它现在的状态（在这篇文章写作时），Yavide看起来像是spf13-vim的下一步。Yavide仍然是在开发阶段并且还没看到他的第一个版本。然而，作者正致力于缺失和被要求的功能。
功能：
对C/C++做了捆绑和调整（计划支持Python和Java）
项目管理和项目浏览
当前单元定义的类符的概览（如，宏，结构体，类，方法，命名空间，等）
源码自动补全（后端有真实的C/C++编译器检查正确性）
源码导航（全自动后端标签系统保证最好的UI体验）
源码静态分析
版本控制集成（集成git）
构建工具（集成make）
为不同的操作定制快捷键
其他特性：
                语法高亮
                高亮当前行
                括号自动补全
                基于上下文的自动补全
                多文本编辑支持
                模板代码补全
                grep支持
                bash集成
                彩色主题支持

插件
下面vim插件是在编写本文时已经集成了插件：
A
Clang_complete
NERDTree
NERDCommenter
SuerTab
Tagbar
vim-airline
UltiSnips
vim-autoclose
vim-fugitive
vim-gitgutter
vim-multiple-cursors
vim-pathogen

安装
在基于Ubuntu的系统上安装Yavide，运行：
```
$ sudo apt-get install build-essential vim-gnome python2.7 git libclang-dev python-dev 
``` 

运行下面的命令从git仓库克隆yavide并安装它：
```
$ cd ~/ && git clone https://github.com/JBakamovic/yavide.git  
$ cd yavide && ./install.sh  
```

修改你想要安装yavide的位置。默认是/opt。yavide将创建它自己的目录并且安装文件在里面。

在GitHub上：Yavide

本文翻译自：YAVIDE: MODERN C C++ IDE OVER VIM

为什么要弄这么一篇文章呢，因为我也想写一个包含各种我需要的插件的vim的IDE，按照我的想法来构建，遵守我的使用习惯，并且在我需要的任何时候我都可以快速的从github上clone下来，完成个性化ide的搭建。this all.