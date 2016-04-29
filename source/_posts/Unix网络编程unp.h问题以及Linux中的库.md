---
title: Unix网络编程unp.h问题以及Linux中的库
date: 2014-03-17 16:27
tags: 网络编程
---

Stevens的《UNIX网络编程 卷1：套接字连网API》是一本很著名的UNIX网络编程书籍。其中使用了一个unp.h的引用，如果没有设置相应的库的话，即使引入了头文件也是没用的。所以首先要配置相应的库。
基础环境：
操作系统 Debian
开发环境 GCC，MAKE等，首先要有c编译器，make等基础的工具。如果没有的话，可以通过下面命令安装。这个命令会安装一个工具集，其中包含了很多linux C开发所需的工具。
```shell
sudo apt-get install build-essential  
```

（1）从网上下载unpv.13e.tar.gz。在网上一搜一大堆。unpv.12e.tar.gz版本的有问题，所以要避免使用。下载了安装包解压后就可以照着他的README文件指示安装了。解压的命令是
```shell
tar -xvf unpv.13e.tar.gz  
```

（2）现在可以cd到unpv13e目录。执行下面两个命令就可以在unpv13e目录下生成相应的库函数。
```
cd unpv13e  
./configure  
cd lib && make  
```

（3）返回unpv13e目录，将生成的libunp.a复制到相应的库文件目录下。相应的shell命令。
```
cd ..  
sudo cp libunp.a /usr/lib    
sudo cp libunp.a /usr/lib64  
```

（4）修改unp.h并将其和config.h拷贝到/usr/iinclude中
```
gedit lib/unp.h   //将unp.h中#include "../config.h"修改为#include "config.h"  
```

```
sudo cp lib/unp.h /usr/include    
sudo cp config.h /usr/include   
```

（5）编写一个小程序测试一下吧，在unpv13e的intro目录中包含了几个实例。可以用来测试一下。看能否生成相应的可执行文件。
```
cd ./intro    
gcc daytimetcpcli.c -o daytimetcpcli -lunp   
```

附录：Linux中的库
库是作为软件的一个组建存在的，其目的是提高软件的可重用性。库里面封装了数据和函数。库可以分为静态库和动态库两种。在Linux系统中，通常把库文件放在/usr/lib或/lib目录中。Linux库文件名由前缀lib、库名以及后缀组成，其中动态库的后缀为.so,而静态库的后缀为.a。

在程序中使用静态库和动态库时，他们载入的顺序是不一样的。静态库的代码在编译是就拷贝到应用程序中，因此当多个应用程序同时引用一个静态库函数时，内存会有调用函数的多个副本。，这样的优点是可以节约编译是的时间。而动态库是在程序开始运行后调用库函数是才被载入的，被调函数在内存中只有一个副本，并且动态库是可以在程序运行期间释放动态库所占内存的。

（1）静态库的创建和使用
创建静态库的步骤如下：
1)在一个头文件中声明静态库所导出的函数。
2)在一个源文件中实现静态库函数所导出的函数。
3)编译源文件，生成可执行代码。
4)将可执行代码所在的目标文件加入到某个静态库中，并在静态库拷贝到系统默认的存放库文件的目录下。
 
编译并添加静态库
```
gcc -o mylib.o -c mylib.c  
ar rcs libmylib.a mylib.o  
```

 
ar命令可以创建和修改静态库。具体参见：man ar

（2)动态库的创建和使用
上面的配置过程创建的就是一个动态库。在编译包含动态库的函数是可以使用“-lunp”（包含上面所编译的库文件）选项。程序将调用动态链接库libunp.so中的函数。在-lunp中，-l为选项，unp为库名。unp是“libunp.so”的中间部分，linux中所以的库都以lib开始，在编译程序时无需带上前缀和后缀。
 
编译并添加动态库
```
gcc -fPIC -o mylib.o -c mylib.c  
gcc -shared -o libmylib.so mylib.o  
```
或
```
gcc -fPIC -shared -o libmylib.so mylib.c  
```