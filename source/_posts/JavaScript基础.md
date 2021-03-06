---
title: JavaScript基础
tags: [JavaScript , Front-End]
---

### 一.JS基本概念

#### 1.JS是什么

- JS是一门运行在浏览器端的脚本语言，负责页面中的行为

#### 2.JS编写的位置

- 标签的指定属性中

  ```javascript
  <button onclick="alert('hello');">我是按钮</button>
  <a href="javascript:alert('aaa');">超链接</a>
  ```

- script标签中

  ```javascript
  <script type="text/javascript">
  	//编写js代码
  </script>
  ```

- 外部js文件中，通过script标签将其引入，script标签一旦引入外部文件了，就不能编写代码了。如果需要就新创建一个script标签

  ```javascript
  <script type="text/javascript" src="文件路径"></script>
  ```

### 二.基本语法

#### 1.输出语句

- alert("要输出的内容")：浏览器窗口弹出警告框
- document.write("要输出的内容")：写入body标签中，并在页面中显示
- console.log("要输出的内容")：写到开发者工具的控制台中

#### 2.注释，变量，标识符

- 注释：单行注释//，多行注释/**/
- 严格区分大小写
- 每条语句以分号结尾，不加会耗费系统资源自动加上
- 自动忽略空格和换行
- 字面量：一些已经固定的值，数字，字符串，true等等，字面量不可改变，不方便，一般不使用
- 变量：用var声明，可用来保存字面量，并且可保存任意的字面量
- 标识符：可以含有字母，数字，下划线，$，不能以数字开头

#### 3.数据类型

- 基本数据类型（String，Number，Boolean，Null，Undefined），引用数据类型（Object）
- String：单引号或双引号表示，注意引号匹配，\为转义字符
- Number：所有整数和浮点数，Number.MAX_VALUE为最大值，值超过最大值类型为Infinity，负数相反。NaN为非法数字
- Boolean：逻辑判断
- Null：空对象，typeof返回object
- Undefined：声明但没有赋值的变量
- Object：对象类型
- **基本数据类型和引用数据类型关系**
  - JS变量都保存到栈内存中，基本数据类型的值直接在栈内存中存储，值与值之间相互独立存在，修改一个变量不会影响其他的变量
  - 对象保存到堆内存中，每创建一个新的对象，就会在堆内存中开辟一个新的空间，而变量中保存的是对象的内存地址(引用)。如果两个变量保存的是同一个对象的引用时，当通过一个变量修改属性时，另一个也会受影响
  - 当比较两个基本数据类型时，比较数据值；当比较两个引用数据类型的值时，比较内存地址

#### 4.类型转换

- 转换为String
  - (强制类型转换)调用被转换数据类型的toString()方法，Null和Undefined没有这个方法
  - (强制类型转换)调用String()函数
  - (隐式)任意数据类型+" "
- 转换为Number
  - (强制类型转换)调用Number()函数，字符串转数字(非法数字->NaN，空串或空格串->0)，boolean转数字(true1，false0)，空值转数字(Null->0)，Undefined转数字(Undefined->NaN)
  - (强制类型转换)调用parseInt()或者parseFloat()，专门用来处理字符串，可将有效的整数位或小数位提取出来
  - (隐式)使用一元运算符+来隐式转换
- 转换为Boolean
  - (强制类型转换)调用Boolean函数，字符串转Boolean(除了空串其他全是true)，数值转Boolean(除了0和NaN其他全是true)，Null、undefined转Boolean(都是false)，object转Boolean(都是true)
  - (隐式)两次非运算!!

#### 5.运算符

- typeof运算符：typeof 变量(返回一个用于描述的类型的字符串)
- 算术运算符：除了加法以外对非Number值进行运算，先转换为Number，加法遇到字符串会拼串，任何值和字符串做加法，都会先转换为字符串
- 逻辑运算符
  - &&：短路运算，第一个值为false，返回第一个值；第一个值为true，返回第二个值
  - ||：短路运算，第一个值为true，返回第一个值；第一个值为false，返回第二个值
- 关系运算符
  - 对非数值的情况比较，会将其转换为数字然后比较，Null转换为0，字母字符串转换为NaN
  - 符号两侧都是字符串，比较第一位字符的Unicode
  - NaN跟任何值比较都是false，也不与任何值相等
  - undefined衍生自null，所以两者相等
  - ===：全等运算符，和相等类似，但类型不同直接返回false
  - !==：不全等运算符，和不等类似，但类型不同直接返回true

#### 6.Unicode编码

- 在字符串中用转义字符输入Unicode编码(\u四位编码)
- 在网页中使用Unicode编码(&#十进制编码)

#### 7.对象

- 对象是引用数据类型

- 分类
  - 内建对象：ES标准定义的对象，在任何ES实现中都可以使用，如Math，Number，String，Boolean等等
  - 宿主对象：由JS运行时环境提供的对象，主要是浏览器提供的对象，如BOM，DOM
  - 自建对象：开发人员自己创建的对象
- 创建对象：var obj = new Object(); or var obj = {属性名:属性值 , 属性名:属性值 ......}(对象字面量的属性名可加引号也可以不加，建议不加，使用特殊名字才要加引号)
- 添加属性：对象.属性名 = 属性值 or 对象["属性名"] = 属性值
- 读取属性：对象.属性名 or 对象["属性名"]，[ ]中可以使用变量
- 删除属性：delete 对象.属性名
- in运算符：检查一个对象中是否含有指定属性，"属性名" in 对象，返回Boolean

####  8.函数

- 函数也是一个对象，具有对象的功能

- 创建函数

  - function 函数名([形参1,形参2...形参N]){语句...}
  - var 函数名 = function([形参1,形参2...形参N]){语句...}

- 调用函数：函数名();

- 调用函数时JS解析器不会检查实参的类型和个数，可以传递任意类型的值

  - 实参数量大于形参，多余实参不会赋值
  - 实参数量小于形参，剩余的形参会被赋值为undefined

- return可以返回任意类型的值，基本函数类型，对象，函数等。不显式声明返回值会返回undefined

- 立即执行函数：一般只调用一次

  ```js
  (function fun(形参){
  	//函数语句...
  })(实参);
  ```

#### 9.方法

- 函数也可以作为对象的属性

- 方法：函数作为对象的属性保存，调用这个函数就被称为调用对象的方法

- 只是名称上的区分，没有本质的区别

  - 对象.方法名();
  - 函数名();

- for-in循环：查看一个对象内部的属性

  ```js
  for(var n in 对象名){
      console.log(对象名[n]);
  }
  ```


#### 10.作用域

- 全局作用域：直接编写在script标签中的JS代码，都在全局作用域中。全局作用域在页面打开时被创建，在页面关闭时被销毁
  - 全局作用域中有个全局对象window，它代表一个浏览器的窗口，由浏览器自动创建
  - 在全局作用域中的创建的变量作为window对象的属性保存，创建的函数作为window对象的方法保存
  - 全局作用域中的变量都是全局变量，在页面的任何部分都可以访问的到
- 函数作用域：
- 变量声明提前：使用var关键字声明的变量，会在所有代码执行之前被声明(不会赋值)
- 函数声明提前：使用函数声明形式创建的函数function 函数(){ }会在所有代码执行之前被创建