---
title: String、StringBuffer、StringBuilder类
tags: Java
---

### 一.字符串常量池

字符串常量池属于Java的静态常量池，即.class文件中的常量池，占用class文件的大部分空间，还保存一些类、方法、接口中的常量。

String.intern()方法：查找常量池中是否存在该字符串，若存在则返回字符串的引用，若不存在则把该字符串添加进常量池并返回引用。

- "abc"和new String("abc")的区别

  在编译时候就能计算出的字符串值，JVM用常量池管理这些字符串

  new一个字符串时，先用常量池管理该字符串，然后再用构造器创建一个新String对象，保存在堆内存中，共产生两个对象

常量池保证相同的字符串直接量只有一个，可以多个引用指向同一个常量。

常量池避免频繁创建销毁对象，实现对象共享，节省内存和运行时间。

```java
	String s1 = "abc";
	String s2 = "ab" + "c";
	String s3 = "ab";
	String s4 = "c";
	String s5 = s3 + s4;
	String s6 = new String("abc");
	System.out.println(s1 == s2);//true
	System.out.println(s1 == s5);//false
	System.out.println(s1 == s5.intern());//true
	System.out.println(s1 == s6);//false
```

==表示比较两个引用变量指向的地址。

s1 == s2：编译器会在编译期间自动把已知字符串拼接好，并放入常量池

s1 != s5：s5不能在编译期间确定，地址不能确定

s1 == s5.intern()：intern方法检测出s5内容存在于常量池中，返回字符串引用

s1 != s6：构造器生成的字符串编译时地址不能确定

### 二.StringBuffer和StringBuilder

StringBuffer对象是一个字符序列可变的字符串

StringBuilder是JDK1.5之后新增的类，操作和StringBuffer基本一样

这两个类都解决了大量拼接字符串时产生很多中间对象而影响性能的问题

区别：StringBuffer线程安全，效率低；StringBuilder线程不安全，效率高，所以StringBuilder使用的多，尤其是单线程应用。

```java
	StringBuilder sb = new StringBuilder();
	for(int i = 0; i < 26; i++) {
		sb.append((char)('a'+i));
	}
	System.out.println(sb);//abcdefghijklmnopqrstuvwxyz
	System.out.println(sb.hashCode());//664740647
	sb.reverse();
	System.out.println(sb);//zyxwvutsrqponmlkjihgfedcba
	System.out.println(sb.hashCode());//664740647
	sb.setCharAt(0, 'A');
	System.out.println(sb);//Ayxwvutsrqponmlkjihgfedcba
	sb.insert(1, "bcd");
	System.out.println(sb);//Abcdyxwvutsrqponmlkjihgfedcba
	sb.delete(0, 4);
	System.out.println(sb);//yxwvutsrqponmlkjihgfedcba
```

几个常用方法：

append()：追加

reverse()：倒序

setCharAt()：修改某位置字符

setLength()：设置字符串长度

insert()：在某位置添加字符或字符串

delete()：删除某位置的字符