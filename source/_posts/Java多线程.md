---
title: Java多线程
tags: Java
---

### 一.基本概念

#### 1.程序，进程，线程

- 程序：用某种语言编写的一组指令集合，一段静态的代码
- 进程：程序的一次执行过程，动态过程，存在生命周期。进程是资源分配的单位，系统为每个进程分配不同的内存区域
- 线程：进程可细分为线程，线程是程序内部一条执行路径。线程是资源分配的最小单位。一个进程中的多个线程共享内存单元和堆，可以访问相同变量和对象，每个线程拥有独立的运行栈和程序计数器pc，切换开销小
- 多线程：一个进程在同一时间并行执行多个线程。多个线程操作共享资源有安全隐患

#### 2.单核CPU，多核CPU

- 单核CPU：一个时间单元内，只能执行一个线程的任务
- 多核CPU：可以真正实现多线程

#### 3.并行，并发

- 并行：多个CPU同时执行多个任务
- 并发：一个CPU同时执行多个任务(资源调度)

#### 4.多线程优势

- 提高程序响应能力，对图形化界面更直观，用户体验好
- 提高CPU利用率
- 改善程序结构，有利于后期维护修改

### 二.线程创建

#### 1.Thread类方法

- start()：启动当前线程；调用当前线程的run()
- run()：通常需要重写此方法，将创建线程要执行的操作声明在此方法中
- currentThread()：静态方法，返回当前代码的线程
- getName()：获取当前线程的名字
- setName()：设置当前线程的名字
- yield()：释放当前CPU的执行权
- join()：调用此函数的线程被阻塞
- stop()：已过时，强制结束当前线程
- sleep(long millitime)：让当前线程睡眠指定时间，在睡眠期间，线程是阻塞状态
- isAlive()：判断线程是否死亡

#### 2.线程优先级

- 优先级
  - MAX_PRIORITY：10
  - MIN_PRIORITY：1
  - NORM_PRIORITY：5
- 设置和获取优先级
  - getPriority()：获取线程优先级
  - setPriority()：设置线程的优先级
- 高优先级的线程要抢占低优先级cpu的执行权。但这只是基于概率来说的，高优先级的线程高概率的情况下被执行，并不意味着只有高优先级的线程执行完以后，低优先级的线程才执行

#### 3.继承Thread类

1. 创建一个继承自Thread类的子类
2. 重写Thread类的run()，将此线程执行的操作声明在run()中
3. 创建Thread类子类的对象
4. 通过此对象调用start()：启动当前线程，调用当前线程的run

- 不能直接通过调用run()启动线程
- 同一个线程不能启动两次，会报illegalThreadStateException异常。需要重新创建一个新的线程对象

#### 4.实现Runnable接口

1. 创建一个实现了Runnable接口的类

2. 实现Runnable接口中的抽象方法run()

3. 创建实现类的对象

4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象

   ```java
   ![ThreadState](D:\blog\source\_posts\Java多线程\ThreadState.png)public Thread(Runnable target) {
       this(null, target, "Thread-" + nextThreadNum(), 0);
   }
   ```

   

5. 通过Thread类的对象调用start()，调用当前线程的run(调用Runnable类型的target的run())

- 开发中优先选择Runnable接口创建多线程，因为实现方式没有类的单继承性的局限性，实现的方式更适合处理多个线程有共享数据的情况。
- 联系：public class Thread implements Runnable
- 相同点：两种方式都需要重写run()，将线程要执行的逻辑声明在run()中

#### 5.实现Callable接口

1. 创建一个实现了Callable的实现类
2. 实现call方法，将此线程需要的操作声明在call()中
3. 创建Callable实现类对象
4. 将Callable实现类对象传递到FutureTask构造器中，创建FutureTask对象
5. 将FutureTask对象传递到Thread构造器中，创建Thread对象并start
6. 获取Callable接口中call()返回值：FutureTask对象的get()就是call()的返回值

```java
		public Object call() throws Exception{
			//线程操作代码
		}

		NumThread numThread = new NumThread();
        FutureTask futureTask = new FutureTask(numThread);
        Thread thread = new Thread(futureTask);
```

- Callable比Runnable强大
  - call()可以有返回值
  - call()可以抛出异常，被外面的操作捕获，获取异常信息
  - Callable支持泛型

#### 6.线程池

- 提高响应速度(减少创建新线程的时间)
- 降低资源消耗(重复利用线程池中的线程，不需要每次都创建)
- 便于管理线程

### 三.线程的生命周期

#### 1.线程状态

- 定义于Thread.State
- 新建：线程对象声明并创建
- 就绪：新建的线程start()后，进入队列等待CPU时间片，但是还没分配到资源
- 运行：就绪的进程被调度，获得CPU资源，run()定义了线程的操作
- 阻塞：被人为挂起或执行输入输出操作，让出CPU并临时终止自己的执行
- 死亡：完成工作或强制终止或异常而导致线程结束

#### 2.状态转换图

![](D:\blog\source\_posts\Java多线程\ThreadState.png)

### 四.线程同步

#### 1.卖票问题

- 三个窗口同时卖票，直到票被卖完

- 问题：卖票过程中，出现了重票、错票(出现了线程安全问题)

- 问题出现的原因：当某个线程操作车票的过程中，尚未操作完成，其他线程参与进来，也操作车票的售卖

- 解决：当一个线程在操作共享变量时，即使线程被阻塞，其他线程也必须等待该线程操作完成之后才能进行操作

- 同步机制解决线程安全问题

  1. 同步代码块

     ```java
     synchronized(锁){
     	//需要被同步的代码
     }
     ```

     任何一个类的对象都可以充当锁，但多个线程必须共用一把锁

     在实现Runnable接口创建多线程的方式中，可以考虑用this充当同步监视器

  2. 同步方法

     ```java
     private synchronized void show()
     ```

     同步方法仍然涉及同步监视器，但不需要显示声明

     非静态的同步方法：同步监视器为this

     静态的同步方法：同步监视器为当前类本身
     
  3. Lock锁

     ```java
     ReentrantLock lock = new ReentrantLock();//声明
     //某个函数体内
     lock.lock();
     try{
         //线程安全代码
     }finally{
         lock.unlock();
     }
     ```

     JDK5.0新增，可以显式使用和释放锁，而synchronized是在代码块执行完后自动释放锁

- 同步可以解决线程安全问题，但同步的代码块只能有一个线程参与，其他线程等待，相当于一个单线程的过程，效率低(局限性)

#### 2.死锁

- 死锁：不同线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁

- 出现死锁后，不会抛出异常，不会出现提示，只是所有线程都处于阻塞状态，无法继续进行下去

  ```java
  	StringBuffer s1 = new StringBuffer();
  	StringBuffer s2 = new StringBuffer();
          new Thread(){
              @Override
              public void run() {
                  synchronized (s1){
                      s1.append("a");
                      s2.append("1");
                      try {
                          Thread.sleep(100);
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      }
                      synchronized (s2){
                          s1.append("b");
                          s2.append("2");
                          System.out.println(s1);
                          System.out.println(s2);
                      }
                  }
  
              }
          }.start();
          new Thread(new Runnable() {
              @Override
              public void run() {
                  synchronized (s2){
                      s1.append("c");
                      s2.append("3");
                      try {
                          Thread.sleep(100);
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      }
                      synchronized (s1){
                          s1.append("d");
                          s2.append("4");
                          System.out.println(s1);
                          System.out.println(s2);
                      }
                  }
              }
          }).start();
  ```

### 五.线程通信

- 涉及方法

  - wait()：当前线程进入阻塞状态，释放同步监视器
  - notify()：唤醒wait的一个线程，如果有多个线程在等待，唤醒优先级高的线程
  - notifyAll()：唤醒所有wait()的线程

- wait()，notify()，notifyAll()这三个方法必须使用在同步代码块和同步方法中

- wait()，notify()，notifyAll()这三个方法的调用者必须是同步代码块或者同步方法中的同步监视器，否则会抛出illegalMonitorStateException异常

- wait()，notify()，notifyAll()这三个方法都定义在java.lang.Object类中

- sleep()和wait()的异同

  **相同点**

  - 两者都会使当前线程阻塞

  **不同点**

  - 声明位置不同：Thread类声明sleep()，Object类声明wait()
  - 调用要求不同：sleep任意场景可调用，wait()必须在同步代码块或者同步方法中
  - 关于是否释放锁：如果两个方法都使用在同步代码块和同步方法中，sleep()不会释放锁，wait()会释放锁

#### 1.生产者与消费者

```java
class Clerk{
    private int productCount = 0;
    public synchronized void produceProduct() {
        if (productCount<20){
            productCount++;
            System.out.println(Thread.currentThread().getName() + "开始生产第" + productCount + "个产品");
            notify();
        }else {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public synchronized void customerProduct() {
        if(productCount>0){
            System.out.println(Thread.currentThread().getName() + "开始消费第" + productCount + "个产品");
            productCount--;
            notify();
        }else {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class Producer extends Thread{
    private Clerk clerk;
    public Producer(Clerk clerk){
        this.clerk = clerk;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "开始生产产品......");
        while(true){
            try {
                sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.produceProduct();
        }
    }
}
class Customer extends Thread{
    private Clerk clerk;
    public Customer(Clerk clerk){
        this.clerk = clerk;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "开始消费产品......");
        while(true){
            try {
                sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.customerProduct();
        }
    }
}
public class ProductTest {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        Producer p1 = new Producer(clerk);
        p1.setName("生产者1:");
        Customer c1 = new Customer(clerk);
        c1.setName("消费者1:");
        Customer c2 = new Customer(clerk);
        c2.setName("消费者2:");
        p1.start();
        c1.start();
        c2.start();
    }
}

```

