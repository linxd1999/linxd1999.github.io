---
title: Java常用类
tags: Java
---

### 一.字符串

#### 1.String类

- String用一对" "来表示

- String声明为final，不可被继承

- String实现了Serializable接口：字符串支持序列化；实现Comparable接口：字符串可比较大小

- String内部定义了final char[] value用于存储字符串数据

- 字符序列不可变(不可变性)

  - 当字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值
  - 当对现有字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的字符串对象
  - 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域

- 通过字面量的方式赋值(区别于new)，此时字符串值声明在字符串常量池中

- 字符串常量池不会存储内容相同的字符串

- 字面量定义和new+构造器定义的字符串有什么区别

  - 字面量定义的字符串存储在常量池中；new的字符串对象在堆中生成，然后在常量池中生成相应的字符串常量，堆中对象指向常量池中的字符串。如果常量池中存在相应字符串，则new的字符串只需要创建一个对象。

- 常量与常量的拼接结果在常量池。且常量池中不会存在相同内容的常量，只要其中有一个是变量，结果就在堆中。加了final关键字的字符串是常量。

- String.intern()方法：将常量池中已经存在的字符串String的内容返回出来

  ```java
          String s1 = "abc";
          String s2 = "def";
          String s3 = "abcdef";
          String s4 = "abc" + "def";
          String s5 = s1 + "def";
          String s6 = "abc" + s2;
          String s7 = s1 + s2;
  
          System.out.println(s3 == s4);//true
          System.out.println(s3 == s5);//false
          System.out.println(s3 == s6);//false
          System.out.println(s3 == s7);//false
          System.out.println(s5 == s6);//false
          System.out.println(s5 == s7);//false
          System.out.println(s6 == s7);//false
  
          String s8 = s5.intern();
          System.out.println(s3 == s8);//true
  ```


#### 2.String常用方法

- int length()：返回字符串的长度： return value.length 

- char charAt(int index)： 返回某索引处的字符return value[index] 

- boolean isEmpty()：判断是否是空字符串：return value.length == 0 

- String toLowerCase()：使用默认语言环境，将 String 中的所有字符转换为小写 

- String toUpperCase()：使用默认语言环境，将 String 中的所有字符转换为大写 

- String trim()：返回字符串的副本，忽略前导空白和尾部空白 

- boolean equals(Object obj)：比较字符串的内容是否相同 

- boolean equalsIgnoreCase(String anotherString)：与equals方法类似，忽略大 小写 

- String concat(String str)：将指定字符串连接到此字符串的结尾。 等价于用“+” 

- int compareTo(String anotherString)：比较两个字符串的大小 

- String substring(int beginIndex)：返回一个新的字符串，它是此字符串的从 beginIndex开始截取到最后的一个子字符串。 

- String substring(int beginIndex, int endIndex) ：返回一个新字符串，它是此字 符串从beginIndex开始截取到endIndex(不包含)的一个子字符串。 

- boolean endsWith(String suffix)：测试此字符串是否以指定的后缀结束 

- boolean startsWith(String prefix)：测试此字符串是否以指定的前缀开始 

- boolean startsWith(String prefix, int toffset)：测试此字符串从指定索引开始的 子字符串是否以指定前缀开始

- boolean contains(CharSequence s)：当且仅当此字符串包含指定的 char 值序列 时，返回 true 

- int indexOf(String str)：返回指定子字符串在此字符串中第一次出现处的索引 

- int indexOf(String str, int fromIndex)：返回指定子字符串在此字符串中第一次出 现处的索引，从指定的索引开始 

- int lastIndexOf(String str)：返回指定子字符串在此字符串中最右边出现处的索引 

- int lastIndexOf(String str, int fromIndex)：返回指定子字符串在此字符串中最后 一次出现处的索引，从指定的索引开始反向搜索 注：indexOf和lastIndexOf方法如果未找到都是返回-1

- String replace(char oldChar, char newChar)：返回一个新的字符串，它是 通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 

- String replace(CharSequence target, CharSequence replacement)：使 用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串

- String replaceAll(String regex, String replacement)：使用给定的replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 

- String replaceFirst(String regex, String replacement)：使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。

- boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式 

- String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串。 

- String[] split(String regex, int limit)：根据匹配给定的正则表达式来拆分此 字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中

#### 3.转换

- String和基本数据类型、包装类之间的转换
  - String转换为基本数据类型、包装类：调用包装类的静态方法parsexxx(str)
  - 基本数据类型、包装类转换为String：调用String重载的valueOf(xxx) or + " "(拼接空串)
- String和char[ ]之间的转换
  - String转换为char[ ]：调用String.toCharArray()
  - char[ ]转换为String：调用new String(char [ ])构造器
- String和Byte[ ]之间的转换
  - String转换为Byte[ ]：调用String.getBytes(["字符集"])
  - Byte[ ]转换为String：调用new String(Byte [ ],[]"字符集"])构造器
  - 编码：字符串=>字节(看得懂=>看不懂的二进制数据)
  - 解码：字节=>字符串(看不懂的二进制数据=>看得懂的字符串)
  - 乱码：编码集和解码集不一致

#### 4.StringBuffer和StringBuilder

- JDK5.0新增类
- String，StringBuffer，StringBuilder异同
  - String：不可变字符序列，底层用char[ ]存储
  - StringBuffer：可变字符序列，线程安全，效率低，底层用char[ ]存储
  - StringBuilder：可变字符序列，线程不安全，效率高，底层用char[ ]存储



### 二.比较器

#### 1.自然排序：Comparable接口

- 像String、包装类等实现了Comparable接口，重写了compareTo(obj)方法，给出了比较两个对象的方式
- 像String、包装类等重写了compareTo()以后，进行从小到大的排列
- 重写compareTo(obj)的规则
  - 如果当前对象this大于形参对象obj，则返回正整数
  - 如果当前对象this小于形参对象obj，则返回负整数
  - 如果当前对象this等于形参对象obj，则返回0
- 对于自定义类，如果需要排序，需要实现Comparable接口，重写compareTo()方法

#### 2.定制排序：Comparator接口

- 当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码， 或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那 么可以考虑使用 Comparator 的对象来排序
- 重写compare(Object o1,Object o2)方法，比较o1和o2的大小
  - o1 > o2，返回正整数
  - o1 < o2，返回负整数
  - o1 == o2，返回0

**区别**

- Comparable是一个类使用的接口，能保证实现这个类的对象在任何位置都可以比较大小，而Comparator接口属于临时比较，有特定要求的排序才会临时使用，一般可以用内部类和匿名类来实现

- 为了保证一个类的对象可以有多样的排序方式，可以同时使用这两种接口。如果传入Comparator接口对象作为参数，则按照定制排序的内容进行排序，如果没有传入Comparator接口对象，则把对象强制转换为Comparable接口类型来进行自然排序

  ```java
  	private int compare(E e1,E e2){
          if (comparator!=null){
              return comparator.compare(e1,e2);
          }
          return ((Comparable<E>)e1).compareTo(e2);
      }
  ```

  



