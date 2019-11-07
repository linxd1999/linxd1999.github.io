### Collection集合

- 集合中只能保存对象的引用变量

  List子接口：有序，可重复集合(ArrayList、LinkedList、Vector)

  Set子接口：无序，不可重复集合(HashSet、LinkedHashSet、TreeSet)

  Queue子接口：先进先出队列

- Lambda表达式遍历集合

  Connection接口继承了Iterable接口

  Java8新增了一个forEach(Consumer action)方法，参数是消费者(函数式接口)

  程序将元素依次传给Consumer的accept()，该方法是接口中唯一的抽象方法

  ```java
  	List<String> list = new ArrayList<>();
  	list.add("hello");
  	list.add("world");
  	list.add("java");
  	list.forEach(obj->System.out.println(obj));
  ```

  类似，Iterator的forEachRemaining(Consumer action)同样可以使用lambda表达式遍历迭代器

  ```java
  	it.forEachRemaining(obj->System.out.println(obj));
  ```

### List集合

- 特点：有序、可重复

- 新增方法：

  ```java
  	List<String> list = new ArrayList<>();
  	list.add("hello");
  	list.add("world");
  	list.add("java");
  	//在指定位置添加指定元素
  	list.add(1, "a");
  	//删除指定位置元素，返回被删除的元素
  	System.out.println(list.remove(2));
  	//修改指定位置的元素，返回被修改的元素
  	System.out.println(list.set(2, "javase"));
  	//返回指定索引处的元素
  	System.out.println(list.get(2));
  	System.out.println(list);
  ```

- 并发修改异常(ConcurrentModificationException)

  产生原因：迭代器遍历过程中，通过集合对象修改了集合中元素的长度导致的迭代器获取元素中预期修改值和实际修改值不一致

  解决：用for循环替代迭代遍历，用get()获取元素

- ListIterator

  List特有的迭代器

  可从任一方向遍历集合

  新增add()方法，且底层经过优化，在迭代遍历中用迭代器增加集合内的元素不会抛出并发修改异常。

  ```java
  	while(it.hasPrevious()) {
  		String s = (String)it.previous();
  		System.out.println(s);
  	}
  ```

- 增强for循环实际上还是Iterator，迭代时也不能修改元素长度

- LinkedList集合

  特有方法：

  ```java
  	public void addFirst(E e)
  	public void addLast(E e)
  	public E getFirst()
  	public E getLast()
  	public E removeFirst()
  	public E removeLast()
  ```

### Set集合

- 特点：无序，不可重复

- 没有带索引的方法，不能用普通for循环遍历

- 哈希值：JDK根据对象的地址或者字符串或者数字算出来的int值

  同一个对象多次调用hashCode()方法返回相同哈希值

  默认情况下，不同对象的哈希值不同，可通过重写hashCode()方法，可以实现让不同对象的哈希值相同

- HashSet集合

  底层数据结构是HashMap，由数组和链表构成

  存取顺序不做保证

  保证元素唯一性：存入元素和以前元素比较哈希值

  - 如果哈希值不同，继续执行，把元素添加入集合

  - 如果哈希值相同，调用equals()方法比较

    返回false，继续执行，把元素添加到集合

    返回true，说明元素重复，不存储

  要保证元素唯一性，需要重写hashCode()和equals()

- LinkedHashSet

  哈希表和链表构成，元素有次序

  元素不可重复

- TreeSet

  元素会按照一定的规则排序，取决于构造方法

  TreeSet()：自然排序

  TreeSet(Comparator comparator)：根据指定的比较器进行排序


- 自然排序Comparable

  元素所属类实现Comparable接口，重写compareTo(T o)方法

  重写方法必须注意按照要求的主要和次要条件来写

  compareTo()方法有this，this在前面表示升序

  ```java
  import java.util.TreeSet;
  
  public class TreeSetDemo {
  	public static void main(String[] args) {
  		TreeSet<Student> ts = new TreeSet<>();
  		Student s1 = new Student("a",29);
  		Student s2 = new Student("c",28);
  		Student s3 = new Student("d",30);
  		Student s4 = new Student("b",33);
  		Student s5 = new Student("c",33);
  		ts.add(s1);
  		ts.add(s2);
  		ts.add(s3);
  		ts.add(s4);
  		ts.add(s5);
  		ts.forEach(obj->System.out.println(obj.getName()+" "+obj.getAge()));
  	}
  }
  
  class Student implements Comparable<Student>{
  	private String name;
  	private int age;
  	public Student(String name,int age) {
  		this.name = name;
  		this.age = age;
  	}
  	public String getName() {
  		return name;
  	}
  	public int getAge() {
  		return age;
  	}
  	@Override
  	public int compareTo(Student o) {
  		int num = this.age-o.age;
  		int num2 = num==0 ? this.name.compareTo(o.name) : num;
  		return num2;
  	}
  }
  ```

- 比较器排序Comparator

  重写Comparator的compare(T o1,T o2)方法

  ```java
  import java.util.*;
  
  public class TreeSetDemo {
  	public static void main(String[] args) {
  		TreeSet<Student> ts = new TreeSet<>(new Comparator<Student>() {
  
  			@Override
  			public int compare(Student o1, Student o2) {
  				int num = o1.getAge()-o2.getAge();
  				int num2 = num==0 ? o1.getName().compareTo(o2.getName()) : num;
  				return num2;
  			}
  			
  		});
  		Student s1 = new Student("a",29);
  		Student s2 = new Student("c",28);
  		Student s3 = new Student("d",30);
  		Student s4 = new Student("b",33);
  		Student s5 = new Student("c",33);
  		ts.add(s1);
  		ts.add(s2);
  		ts.add(s3);
  		ts.add(s4);
  		ts.add(s5);
  		ts.forEach(obj->System.out.println(obj.getName()+" "+obj.getAge()));
  	}
  }
  
  class Student{
  	private String name;
  	private int age;
  	public Student(String name,int age) {
  		this.name = name;
  		this.age = age;
  	}
  	public String getName() {
  		return name;
  	}
  	public int getAge() {
  		return age;
  	}
  }
  ```


### Map集合

- 将键映射到值的对象，不能包含重复键，键可以映射最多一个值
- 添加元素时，如果键一样，值会被覆盖
- 