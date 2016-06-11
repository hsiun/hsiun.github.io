---
title:  raspberry内核编译和模块开发
date: 2016-06-11 22:41
tags: linux驱动编程
---

因为打算学一学linux驱动编程了，特意买了一块raspberry来作为开发环境。直接通过镜像按照的内核并不能直接用来做模块开发，因为发行版自身是不带内核源码树的，但是模块开发这个是必要的。所有要进行内核模块开发第一步就是要编译内核。具体到不同版本的raspberry，编译方法有细微的差别，这个raspberry官方有一篇文档专门将这个。我这个内核的编译过程主要就是参考这篇 [KERNEL BUILDING](https://www.raspberrypi.org/documentation/linux/kernel/building.md) 文章的。内核编译有两种方法，一种是在raspberry本地编译，另一种是在另外一台机器上编译好然后考到raspberry的内存卡上。直接在raspberry上编译编译比较简单但是比较花时间，我编译花了大概一个多小时，我个人认为这速度还可以：）。另一种是在pc上交叉编译，得益于官方提供的交叉编译工具，raspberry kernel在pc上的编译过程也比较简单。我这里记录下我在raspberry上编译的过程。

### 内核编译 ###

1、下载linux内核源码，我直接放在用户主目录了，这个在编译模块的时候是会用到的。
```
git clone --depth=1 https://github.com/raspberrypi/linux
```

2、 安装缺少的依赖。
```
sudo apt-get install bc
```

3、 内核配置，pi1和pi2,3不一样。我这里的是pi3，所以我只说明pi3的，pi1参考官方文档。
```
cd linux
KERNEL=kernel7
make bcm2709_defconfig
```

4、 编译安装内核和模块。

```
make -j4 zImage modules dtbs
sudo make modules_install
sudo cp arch/arm/boot/dts/*.dtb /boot/
sudo cp arch/arm/boot/dts/overlays/*.dtb* /boot/overlays/
sudo cp arch/arm/boot/dts/overlays/README /boot/overlays/
sudo scripts/mkknlimg arch/arm/boot/zImage /boot/$KERNEL.img
```

5、 reboot或者开机重启下，使用uname看下内核版本是否发生了变化。


### 模块开发 ###
我这里使用的是《LINUX设备驱动程序》这本书上的例子来做的。下面是一个过程的 记录。
1、 使用编辑软件编辑模块源代码，我的是hello.c
```
#include <linux/init.h>
#include <linux/module.h>

MODULE_LICENSE("Dual BSD/GPL");

static int hello_init(void)
{
    printk(KERN_ALERT "Hello, world\n");
    return 0;
}

static void hello_exit(void)
{
    printk(KERN_ALERT "Goodbye, cruel world\n");
}

module_init(hello_init);
module_exit(hello_exit);

```

2、 编辑Makefile文件，我这个比较简单只是用来测试用的。
```
obj-m := hello.o
```

3、 在命令行中编译模块。-C参数指定内核源码树位置，M为模块源码所在目录。modules没搞明白是干嘛的。
```
make -C ~/linux M=/home/pi/workspace modules
```

4、 模块的安装卸载及其输出的查看。
```
insmod ./hello.ko
rmmod hello
tail -2 /var/log/syslog
```

第一个简单的模块就这样编译完了，有了这些准备为我们后面的模块开发做准备。另外一些模块开发环境的配置方法可以参考[这篇](http://stackoverflow.com/questions/20167411/how-to-compile-a-kernel-module-for-raspberry-pi)
