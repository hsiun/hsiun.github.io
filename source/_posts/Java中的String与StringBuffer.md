---
title: Java中的String与StringBuffer 
date: 2013-12-11 11:32
tags:  Java编程学习
---

以前学java的时候竟然不知道有一个StringBuffer，终于有一天需要用函数来改变String的值得时候。才找到一些资料提到StringBuffer，这是前言。
String是使用java.lang包中的String类来创建的，因此字符串变量是类类型的，是一个对象。字符串常量是对象，因此可以把字符串常量的引用付给一个字符串变量。所以：
```java
String s；
s="How are you";
s="How do you do";
```
执行结果打印：s="How do you do";
但是s[3]=a;这样的赋值方式是错误的。
```java
public class TestFun2 {
   public static void testStr(String str){
   str="hello";//型参指向字符串 “hello” 
} 
public static void main(String[] args) {
   String s="1" ; 
   TestFun2.testStr(s);
   System.out.println("s="+s); //实参s引用没变，值也不变 
   }
}
```
执行结果打印：s=1
前面的例子表明String类创建的字符串对象是不可修改的。也就是说String字符串不能修改。删除或替换字符串中的某个字符，即String对象一旦创建，实体是不可再发生变化的，但StringBuffer类可以。该类能创建可修改的字符串序列，即该对象的实体的内存是可以自动改变大小的。
例如，StringBuffer有append()方法。又如上例如果传入的参数是StringBuffer类型的则其内容是可修改，可返回的。
 