---
title: Set & Map
tags:
	-Java
	-DataStructure
---



### 一.集合(Set)

- 不能存放重复的元素
- 一般用于去重
  - 存放新增ip，统计新增ip量
  - 存放词汇，统计词汇量

- 接口

  ```java
  public interface Set<E> {
      int size();
      boolean isEmpty();
      void clear();
      boolean contains(E element);
      void add(E element);
      void remove(E element);
      void traversal(Visitor<E> visitor);
      public static abstract class Visitor<E> {
          boolean stop;
          public abstract boolean visit(E element);
      }
  }
  ```

- 可使用：动态数组(ArrayList)，链表(LinkedList)，AVL树，红黑树等来实现Set集合

#### 1.TreeSet

- ListSet底层用链表实现，TreeSet底层用红黑树实现。
- TreeSet性能优于ListSet
- JDK中没有ListSet
- TreeSet缺陷：元素必须具备可比较性

#### 2.HashSet

#### 3.LinkedHashSet



### 二.映射(Map)

- 在有些语言中也叫字典(dictionary)

- Map的每个key是唯一的，对应一个value(键值对)

- Map也可以用链表，二叉搜索树(AVL，红黑树)等数据结构来实现

- 接口

  ```java
  public interface Map<K,V> {
      int size();
      boolean isEmpty();
      void clear();
      V put(K key,V value);
      V get(K key);
      V remove(K key);
      boolean containsKey(K key);
      boolean containsValue(V value);
      void traversal(Visitor<K,V> visitor);
      public static abstract class Visitor<K,V>{
          boolean stop;
          public abstract boolean visit(K key, V value);
      }
  }
  ```

#### 1.TreeMap

- 实现
  - 可以用内部类封装键值对，但不好用
  - 在把TreeSet类当成红黑树类来实现，节点中存储<K,V>的键值对
- TreeMap中的key集合就是一个TreeSet。所以TreeMap可以转化成TreeSet
  - 节点中存储的数据为<E,Object>，其中Object赋值为空对象
  - 会造成性能(空间)浪费
  - 优点：可以把Set和Map代码集成起来，减少代码量
- 平均时间复杂度：添加，搜索，删除都是O(logn)
- 如果不考虑Map中存储元素的顺序和key的可比较性，有更好的实现方案，哈希表可以将时间复杂度缩小到O(1)

#### 2.HashMap

#### 3.LinkedHashMap

### 三.哈希表

#### 1.哈希表

- 哈希表也叫散列表
- 添加，搜索，删除的流程类似
  - 利用哈希函数生成的key对应的index[O(1)]
  - 根据index操作定位数组元素[O(1)]
- 哈希表是典型的以**空间换时间**的应用
- 哈希表内部的数组元素，也称为Bucket(桶)

#### 2.哈希冲突

- 哈希冲突(Hash Collision)：也叫哈希碰撞，2个或以上不同的key，经过哈希函数计算出相同的结果
- 解决
  - 开放定址法：按照一定规则向其他地址探测，直到遇到空桶
  - 再哈希法：设计多个哈希函数
  - 链地址法：通过链表将同一个index的元素串起来
- JDK1.8的哈希冲突解决方案(单向链表+红黑树)
  - 默认使用单向链表将元素串起来
  - 在添加元素时，可能会由单向链表转为红黑树来存储元素；在红黑树节点少到一定程度时，又转化成单向链表
  - 使用单向链表的原因：每次都从头开始遍历，key值相等覆盖，相比于双向链表少一个指针，节省内存空间

#### 3.哈希函数

- 哈希函数：生成元素存储的索引值

  - 先生成key的哈希值(必须是整数)
  - 再让key的哈希值跟数组的大小进行相关运算，生成一个索引值

- 为了提高效率，可以使用&位运算取代&运算(前提：数组长度为2的幂)

  ```java
  public int hash(Object key){
      return hash_code(key) % table.length;
  }
  
  //改良版本
  public int hash(Object key){
      return hash_code(key) & (table.length-1);
  }
  ```

- 数组长度为2^n，则2^n-1二进制为全1。无论key的值多大，&(2^n-1)的结果范围都是0-2^n-1。

- 如果执意想用%来算索引值，将数组长度设计为素数，可以大大减少哈希冲突

- 良好的哈希函数：让哈希值分布更均匀，减少哈希冲突次数，提升哈希表性能

#### 4.hashCode()

- key的常见种类：整数，浮点数，字符串，自定义对象

- 不同种类的key，哈希值的生成方式不同，但目标一致

  - 尽量让每个key的哈希值唯一
  - 尽量让key的所有信息参与运算

- 整数：整数值当做哈希值

- 浮点数：将存储的二进制格式转为整数值，floatToIntBits(value)

- Long和Double类型：让前32位二进制位和后32位二进制位混合运算

  ```java
  public static int hashCode(long value){
      return (int)(value ^ (value >>> 32));
  }
  
  public static int hashCode(double value){
      long bits = doubleToLongBits(value);
      return (int)(value ^ (value >>> 32));
  }
  //^：异或运算符(使用&，|都会使数据不能充分混合计算，容易出现全0或全1的情况)
  //异或运算符的作用：保证对象有一位改变则哈希值就会改变，index进而改变，尽可能减少碰撞
  //>>>：无符号右移，剩下用0填充
  ```

- 字符串：字符串由若干字符组成，而字符的本质就是整数
  - 5489 = 5* 10^3+4* 10^2+8* 10^1 +9 *10^0
  - jack = j* n^3+a* n^2+c* n^1 +k *n^0 = [(j *n+a) *n+c] *n+k
  - 在JDK中，乘数n=31,31是一个奇素数，JVM会将31 *i优化成(i<<5)-i
  
- 自定义对象：默认哈希值由内存地址决定，内存地址相同的对象哈希值才相同。这意味着两个不同的对象，对象内的所有内容都相同，但这两个对象的哈希值是不同的。所以实际开发中需要重写对象的hashCode()方法

- 哈希值相同，索引一定相同；哈希值不同，索引也可能相同

- 扰动计算：尽量运用前一半二进制位和后一半二进制位做异或运算，提高key分布的稳定性

#### 5.equals()

- 用于比较：发生哈希冲突时，两个对象的key是否相同(决定是否覆盖)
- 同时实现hashCode()和equals()方法才能使哈希表稳定
- 只实现equals()：hashCode()不同但是索引可能相同，如果索引相同且对象类型、存储数据都相同，这两个对象会被哈希表存储在同一个位置(相互覆盖)，但是几率很小，所以这种哈希表不稳定
- 只实现hashCode()：如果不实现equals()，默认只当两个对象内存地址相同时，两个对象才相等。所以两个对象类型、存储数据都相同的对象会被存放在哈希表同一个索引下的不同位置
- 如果自定义对象要作为哈希表的key，则hashCode()，equals()都必须实现
- 两个对象是否相等取决于需求，可能成员变量不用完全相同也可以判定为两个对象相等
- 性质
  - 自反性：对于任何非 null 的 x，x.equals(x)必须返回true
  - 对称性：对于任何非 null 的 x、y，如果 y.equals(x) 返回 true，x.equals(y) 必须返回 true
  - 传递性：对于任何非 null 的 x、y、z，如果 x.equals(y)、y.equals(z) 返回 true，那么x.equals(z) 必须 返回 true
  - 一致性：对于任何非 null 的 x、y，只要 equals 的比较操作在对象中所用的信息没有被修改，多次调用 x.equals(y) 就会一致地返回 true，或者一致地返回 false 
  - 对于任何非 null 的 x，x.equals(null) 必须返回 false

#### 6.添加、查找

- 添加和查找都需要利用key找到hashCode，进而找到index。然后在红黑树上考虑插入某处位置
- 哈希表的元素可为不同类型，可为空对象，也可以是不可比较的

#### 7.扩容

- 装填因子(Load Factor)：节点总数量/哈希表桶数组长度，也叫负载因子
- JDK1.8的HashMap中，装填因子超过0.75，数组长度扩容为原来两倍

- 当扩容为原来容量的两倍时，节点的索引有两种情况
  - 保持不变
  - index = index + table.length(旧数组长度)
- 每次插入元素时先判断，如果装填因子超过0.75，则进行扩容

#### 8.HashMap和TreeMap

- TreeMap: O(logn)
- HashMap: O(1)，由于哈希值处理，负载因子等原因，树高很小，可认为定位的时间复杂度为O(1)
- 选择TreeMap：元素具备可比较性且要求升序遍历(元素从小到大)
- 选择HashMap：无序遍历

#### LinkedHashMap

- 在HashMap的基础上维护元素的添加顺序，使得遍历的结果是遵从添加顺序的