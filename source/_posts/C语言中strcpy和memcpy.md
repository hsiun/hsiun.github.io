---
title: C语言中strcpy和memcpy
date: 2014-04-22 08:31
tags: C语言学习
---
strcpy和memcpy都是标准C库函数，且都可用于字符串复制。

strcpy提供了字符串的复制。即strcpy只用于字符串复制，并且它不仅复制字符串内容之外，还会复制字符串的结束符。strcpy的复制正是在遇到字符串的结束符时终止的。
memcpy提供了一般内存的复制。即memcpy对于需要复制的内容没有限制，因此用途更广。其结束以参数n的大小为准。

其所包含的头文件，和函数声明如下       
```
#include <string.h>  
  
char *strcpy(char *dest, const char *src);  
char *strncpy(char *dest, const char *src, size_t n);  
```

```
#include <string.h>  
  
void *memcpy(void *dest, const void *src, size_t n);  
```



strcpy和memcpy主要有以下3方面的区别。
1、复制的内容不同。strcpy只能复制字符串，而memcpy可以复制任意内容，例如字符数组、整型、结构体、类等。
2、复制的方法不同。strcpy不需要指定长度，它遇到被复制字符的串结束符"\0"才结束，所以容易溢出。memcpy则是根据其第3个参数决定复制的长度。
3、用途不同。通常在复制字符串时用strcpy，而需要复制其他类型数据时则一般用memcpy