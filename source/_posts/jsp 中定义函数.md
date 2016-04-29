---
title: jsp 中定义函数
date: 2013-12-11 23:16
tags: Web基础
---

jsp 中定义函数

在一个jsp文件中定义一个java函数的方法，我知道的是有两种的。
方法一
第一种是使用<%! %>。即在普通jsp标记之中使用一个另外的<%! %>便可以了。
```jsp
<% !
   public String function(){
   return "true";
  }
%>
```
方法二
第二种是定义一个类,在类里定义方法,再调用方法就可以了。其实这样相当于是定义了外部的类再引用外部的类中的方法而已。
结构如下,不过这些代码最好还是写在.java的文件里,写在jsp中不好
```jsp
<%
class test{
   public String a(){
   return "true";
   }
}
%>
 
<%
test t = new test();
String a = t.a();
%>
<%=a%>

```
这两种方法我只用过第一种，第二种没用过，但总结一下记一下，以后学到或用到在来看。