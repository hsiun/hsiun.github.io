---
title: 使用git上传已有的代码到github上
date: 2014-04-14 22:42
tags: 开发工具
---

1、使用已有的github账户，在上面创建一个项目。进入github官网后在右下角，有一个名为New repository的绿色按钮，点击便可创建一个github项目。


2、然后就是填写一些相关的信息了。按照自己的想法填就是了。

3、创建SSH密匙
在shell下，输入以下命令。
```
ssh-keygen -C 'your@email.address' -t rsa  
```
确认使用默认路径，然后输入两次你要是用的密码就行（一般直接敲几个回车不使用密码）

4、提交密匙
现在又要回到github的页面上，在右上方工具栏里找到Account Settings。在这个页面上有一个SSH Keys标签，选择Add SSH key。Title随便取，Key就是开始生成的一段东西。找到id_rsa.pub文件（一般是在用户的主目录下），把它打开可以看到一堆文字，拷贝下来黏贴到github页面key的空白处。然后Apply，就好了。

可以使用以下命令测试连接
```
ssh -v git@github.com  
```

5、上传代码
最后就是上传你的代码了~ bash切换到你的项目目录下，输入以下命令。
```
git remote add origin git@github.com:你的github用户名/你的github项目名.git  
git push origin master  
```
现在再去http://github.com/你的github用户名/你的github项目 就可以看到你的项目了~ Good luck
当然这是从无到有，如果你有一个Git的repo，想添加到github上，那就直接使用第8步的命令就可以了（不要忘记密匙的相关工作）。话说很多初学者应该会和我一样，在初期搞不清git和github的关系，git是和CVS,SVN并列的一个概念，而github是和Google Code, sourceforge并列的一个概念，在具体点说，git是一个代码管理工具，而github是一个代码托管平台，这样说就明白了吧。所以，git的学习的话，参见这里。
