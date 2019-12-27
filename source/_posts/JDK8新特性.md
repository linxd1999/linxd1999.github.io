---
title: JDK8新特性
---

### Lambda表达式

#### 1.定义

- Lambda 是一个匿名函数，我们可以把 Lambda 表达式理解为是一段可以传递的代码（将代码像数据一样进行传递。使用它可以写出更简洁、更 灵活的代码。作为一种更紧凑的代码风格

#### 2.格式

- ->：lambda操作符或箭头操作符
  - 左侧：lambda形参列表(接口中抽象方法的形参列表)
  - 右侧：lambda体(重写抽象方法的方法体)
- 本质：作为接口的实例(java万物皆对象)，lambda表达式也是一个对象

<!--more-->

#### 3.语法

##### 3.1 无参，无返回值

```java
Runnable r = () -> {System.out.println("helloworld")};
```

##### 3.2 需要一个参数，但没有返回值

```java
Consumer<String> c = (String str) -> {System.out.println("helloworld")};
```

##### 3.3 类型推断

- 通过泛型来进行类型推断，省略形参的类型

```java
Consumer<String> c = (str) -> {System.out.println("helloworld")};
```

##### 3.4 单形参

- 括号可省略

```
Consumer<String> c = str -> {System.out.println("helloworld")};
```

##### 3.5 多形参，多执行语句

```java
Comparator<Integer> c = (o1, o2) -> {
    System.out.println("helloworld")
    Integer.compare(o1,o2);
};
```

##### 3.6 单执行语句

- 大括号和return都可以省略，且必须同时省略或不省略

```java
Comparator<Integer> c = (o1,o2) -> Integer.compare(o1,o2);
```

