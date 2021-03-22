---
title: java面试笔记
date: 2021-03-18 10:06:55
tags:
- java
- 面试
categories: java
description: java面试笔记整理
top_img:
cover: https://gitee.com/xyming108/blog_img/raw/master/wallpaper/b10.jpg
---

[TOC]



### 面向过程(POP)与面向对象(OOP)

- 面向过程，强调的是功能行为，以函数为最小单位，考虑怎么做。

- 面向对象，将功能封装进对象，强调具备了功能的对象，以类/对象为最小单位，考虑谁来做。面向对象更加强调运用人类在日常的思维逻辑中采用的思想方法与原则，如抽象、分类、继承、聚合、多态等。易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统更加灵活、更加易于维护。

### 对象内存解析：堆、栈、方法区
- 堆（Heap），此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。这一点在Java虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配。 通常所说的栈（Stack），是指虚拟机

- 栈，（Stack）虚拟机栈用于存储局部变量等。局部变量表存放了编译期可知长度的各种基本数据类型（boolean、byte、char 、 short 、 int 、 float 、 long 、double）、对象引用（reference类型，它不等同于对象本身，是对象在堆内存的首地址）。 方法执行完，自动释放。

- 方法区（Method Area），用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

### 局部变量和成员变量的区别

|              | **成员变量**                     | **局部变量**                             |
| ------------ | -------------------------------- | ---------------------------------------- |
| 声明的位置   | 直接声明在类中                   | 方法形参或内部、代码块内、构造器内等     |
| 修饰符       | private、public、static、final等 | 不能用权限修饰符修饰，可以用final修饰    |
| 初始化值     | 有默认初始化值                   | 没有默认初始化值，必须显式赋值，方可使用 |
| 内存加载位置 | 堆空间 或 静态域内               | 栈空间                                   |

### 高内聚、低耦合

- 高内聚 ：类的内部数据操作细节自己完成，不允许外部干涉。
- 低耦合 ：仅对外暴露少量的方法用于使用。

### 四种访问权限修饰符

|     修饰符      | 类内部 | 同一个包 | 不同包的子类 | 同一个工程 |
| :-------------: | :----: | :------: | :----------: | :--------: |
|     private     |  Yes   |          |              |            |
| default（缺省） |  Yes   |   Yes    |              |            |
|    protected    |  Yes   |   Yes    |     Yes      |            |
|     public      |  Yes   |   Yes    |     Yes      |    Yes     |

### this和super的区别

| No.  | 区别点     | this                                                   | super                                    |
| ---- | ---------- | ------------------------------------------------------ | ---------------------------------------- |
| 1    | 访问属性   | 访问本类中的属性，如果本类没有此属性则从父类中继续查找 | 直接访问父类中的属性                     |
| 2    | 调用方法   | 访问本类中的方法，如果本类没有此方法则从父类中继续查找 | 直接访问父类中的方法                     |
| 3    | 调用构造器 | 调用本类构造器，必须放在构造器的首行                   | 调用父类构造器，必须放在子类构造器的首行 |

### 方法重载和重写的区别

- 重载：

  发生在同一个类中，方法名必须相同，参数的类型、个数、顺序至少要有一项不同，方法的返回值和访问修饰符可以不同，发生在编译时。

- 重写：

  发生在父类和子类中。

  子类重写的方法和父类被重写的方法要有相同的方法名、参数列表。

  子类重写的方法的返回值类型不能大于父类被重写的方法的返回值类型。

  子类重写的方法使用的访问权限不能小于父类被重写的方法的访问权限。

  子类方法抛出的异常不能大于父类被重写方法的异常。

  子类不能重写父类中声明为private权限的方法。

  发生在运行时，动态绑定，只有等到方法调用的那一刻，解释运行器才会确定所要调用的具体方法。

### ==  操作符与 equals 方法

- == 既可以比较基本类型也可以比较引用类型。对于基本类型就是比较值，对于引用类型就是比较内存地址
- equals 只能用于比较引用类型，比较是否指向同一个对象。
  - 特例：当用 equals() 方法进行比较时，对类File、String、Date及包装类（Wrapper Class）来说，是比较类型及内容而不考虑引用的是否是同一个对象；
  - 原因：在这些类中重写了 Object 类的 equals() 方法。
- 具体要看自定义类里有没有重写Object的equals方法来判断。
- 通常情况下，重写equals方法，会比较类中的相应属性是否都相等。

### static

被修饰后的成员具备以下特点：
- static可以修饰属性、 方法、代码块、内部类
- 随着类的加载而加载
- 优先于对象存在
- 修饰的成员，被所有对象所共享
- 访问权限允许时，可不创建对象，直接被类调用
- 在static方法内部只能访问类的static修饰的属性或方法，不能访问类的非static的结构
- 因为不需要实例就可以访问static方法，因此static方法内部不能有this，也不能有super
- 没有对象的实例时，可以用 "类名.方法名()" 的形式访问由static修饰的类方法

### final
- final 标记的类不能被继承。提高安全性，提高程序的可读性。 
- final 标记的方法不能被子类重写
- final 标记的变量(成员变量或局部变量)即称为常量，名称大写，且只能被赋值一次
  - final 标记的成员变量必须在声明时或在每个构造器中或代码块中显式赋值，然后才能使用。 
  - 例如：final double MY_PI = 3.14

### 抽象类和抽象方法

- 用abstract关键字来修饰一个类，这个类叫做抽象类；用abstract来修饰一个方法，该方法叫做抽象方法。 
- 抽象方法：只有方法的声明，没有方法的实现。以分号结束：比如：public abstract void talk()
- 含有抽象方法的类必须被声明为抽象类。
- 抽象类不能被实例化。抽象类是用来被继承的，抽象类的子类必须重写父类的抽象方法，并提供方法体。若没有重写全部的抽象方法，仍为抽象类。
- 不能用abstract修饰变量、代码块、构造器；
- 不能用abstract修饰私有方法、静态方法、final的方法、final的类。

### interface

- 接口(interface)是抽象方法和常量值定义的集合。 

- 接口的特点：
  - 用interface来定义。 
  - 接口中的所有成员变量都默认是由public static final修饰的。 
  - 接口中的所有抽象方法都默认是由public abstract修饰的。 
  - 接口中没有构造器。 
  - 接口采用多继承机制。

### 接口和抽象类的对比
| No.  | 区别点       | 抽象类                                                       | 接口                                                         |
| ---- | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1    | 定义         | 包含抽象方法的类                                             | 主要是抽象方法和全局常量的集合                               |
| 2    | 组成         | 构造方法、抽象方法、普通方法、                               | 常量、变量 常量、抽象方法、(jdk8.0:默认方法、静态方法)       |
| 3    | 使用         | 子类继承抽象类(extends)                                      | 子类实现接口(implements)                                     |
| 4    | 关系         | 抽象类可以实现多个接口                                       | 接口不能继承抽象类，但允许继承多个接口                       |
| 5    | 常见设计模式 | 模板方法                                                     | 简单工厂、工厂方法、代理模式                                 |
| 6    | 对象         | 都通过对象的多态性产生实例化对象                             | 都通过对象的多态性产生实例化对象                             |
| 7    | 局限         | 抽象类有单继承的局限                                         | 接口没有此局限                                               |
| 8    | 实际         | 作为一个模板                                                 | 是作为一个标准或是表示一种能力                               |
| 9    | 选择         | 如果抽象类和接口都可以使用的话，优先使用接口，因为避免单继承的局限 | 如果抽象类和接口都可以使用的话，优先使用接口，因为避免单继承的局限 |

### 抛出异常

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210228172449935.png" />

### 15、API 中创建线程的方式

- 方式一：继承Thread类

  - 定义子类继承Thread类

  - 子类中重写Thread类中的run方法

  - 创建Thread子类对象，即创建了线程对象

  - 调用线程对象start方法：启动线程，调用run方法

  - 一个线程对象只能调用一次start()方法启动，如果重复调用了，则将抛出以上

    的异常 "IllegalThreadStateException"

  - Thread 类的有关方法

    - void start()：启动线程，并执行对象的run()方法
    - run()：线程在被调度时执行的操作
    - String getName()：返回线程的名称
    - void setName(String name)：设置该线程名称
    - static Thread currentThread()：返回当前线程。在Thread子类中就是this，通常用于主线程和Runnable实现类
    - static void yield()：线程让步
      - 暂停当前正在执行的线程，把执行机会让给优先级相同或更高的线程
      - 若队列中没有同优先级的线程，忽略此方法
    - join() ：当某个程序执行流中调用其他线程的 join() 方法时，调用线程将被阻塞，直到 join() 方法加入的 join 线程执行完为止
      - 低优先级的线程也可以获得执行
    - static void sleep(long millis)：(指定时间:毫秒) 
      - 令当前活动线程在指定时间段内放弃对CPU控制,使其他线程有机会被执行,时间到后重排队。
      - 抛出InterruptedException异常
    - stop()：强制线程生命期结束，不推荐使用
    - boolean isAlive()：返回boolean，判断线程是否还活着

- 方式二：实现 Runnable 接口

  - 定义子类，实现Runnable接口
  - 子类中重写Runnable接口中的run方法
  - 通过Thread类含参构造器创建线程对象
  - 将Runnable接口的子类对象作为实际参数传递给Thread类的构造器中
  -  调用Thread类的start方法：开启线程，调用Runnable子类接口的run方法

- 方式三：实现Callable接口（JDK5.0新增线程创建方式）

  - 与使用Runnable相比， Callable功能更强大些
    - 相比run()方法，可以有返回值
    - 方法可以抛出异常
    - 支持泛型的返回值
    - 需要借助FutureTask类，比如获取返回结果
      - Future接口
        - FutrueTask是Futrue接口的唯一的实现类
        - 可以对具体Runnable、Callable任务的执行结果进行取消、查询是否完成、获取结果等。
        - FutureTask 同时实现了Runnable, Future接口。它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值

- 方式四：使用线程池

  - 背景：经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大。 
  - 思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中的公共交通工具。
  - 好处：
    - 提高响应速度（减少了创建新线程的时间）
    - 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
    - 便于线程管理
      - corePoolSize：核心池的大小
      - maximumPoolSize：最大线程数 
      - keepAliveTime：线程没有任务时最多保持多长时间后会终止
      - …

### 多线程的安全问题解决方式：同步机制

- synchornized锁：
  - 同步代码块：synchornized( 对象 ) { 需要被同步的代码; }
  - synchronized 还可以放在方法声明中，表示整个方法为同步方法
  - 当前线程在同步代码块、同步方法中执行了线程对象的 wait() 方法，当前线程暂停，并释放锁
  - 线程执行同步代码块或同步方法时，程序调用Thread.sleep()、Thread.yield()方法暂停当前线程的执行，但不释放锁
- Lock锁：
  - 开启锁：lock.lock();
  - 关闭锁：lock.unlock();

### synchronized与Lock的对比

- Lock是显式锁（手动开启和关闭锁，别忘记关闭锁），synchronized是隐式锁，出了作用域自动释放
- Lock只有代码块锁，synchronized有代码块锁和方法锁
- 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）

### 对比String、StringBuffer、StringBuilder

- String(JDK1.0)：不可变字符序列
- StringBuffer(JDK1.0)：可变字符序列、效率低、线程安全
- StringBuilder(JDK 5.0)：可变字符序列、效率高、线程不安全
- 注意：作为参数传递的话，方法内部String不会改变其值，StringBuffer和StringBuilder会改变其值。

### comparable和comparator

- ccomparable：实现 Comparable 的类必须实现 compareTo(Object obj) 方法，两个对象即通过 compareTo(Object obj) 方法的返回值来比较大小。如果当前对象this大 于形参对象obj，则返回正整数，如果当前对象this小于形参对象obj，则返回负整数，如果当前对象this等于形参对象obj，则返回零。
- comparator：重写compare(Object o1,Object o2)方法，比较o1和o2的大小：如果方法返回正整数，则表示o1大于o2；如果返回0，表示相等；返回负整数，表示o1小于o2。

### Collection 和 Map 

Collection接口：单列数据，定义了存取一组对象的方法的集合

		List：元素有序、可重复的集合
	
		Set：元素无序、不可重复的集合


Map接口：双列数据，保存具有映射关系“key-value对”的集合

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210228214649768.png" />

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210228214712019.png" />

### ArrayList、LinkedList、Vector 比较

- List集合类中元素有序、且可重复，集合中的每个元素都有其对应的顺序索引

- JDK1.7：ArrayList像饿汉式，直接创建一个初始容量为10的数组

- JDK1.8：ArrayList像懒汉式，一开始创建一个长度为0的数组，当添加第一个元素时再创建一个始容量为10的数组

- ArrayList和LinkedList的异同

  二者都线程不安全，比线程安全的Vector执行效率更高。

  此外，ArrayList是实现了基于动态数组的数据结构，LinkedList基于双向链表的数据结构。

  对于随机访问get和set，ArrayList绝对优于LinkedList，因为LinkedList要移动指针。

  对于新增和删除操作add(特指插入)和remove，LinkedList比较占优势，因为ArrayList要移动数据。

- ArrayList和Vector的区别

  Vector和ArrayList几乎是完全相同的,唯一的区别在于Vector是同步类(synchronized)，属于强同步类。因此开销就比ArrayList要大，访问要慢。正常情况下,大多数的Java程序员使用ArrayList而不是Vector,因为同步完全可以由程序员自己来控制。Vector每次扩容请求其大小的2倍空间，而ArrayList是1.5倍。Vector还有一个子类Stack

### HashSet

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302202652221.png" />

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302202714926.png" />

### HashMap

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302203048564.png" />

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302203125800.png" />

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302203316725.png" />

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302203414123.png" />

### HashMap与HashTable

<img src="https://gitee.com/xyming108/blog_img/raw/master/post/image-20210302203503624.png" />

### 红黑树

https://www.yycoding.xyz/post/2014/3/27/introduce-red-black-tree



### InnoDB，MyISAM区别

1）InnoDB支持事务，MyISAM不支持，这一点是非常之重要。事务是一种高级的处理方式，如在一些列增删改中只要哪个出错还可以回滚还原，而MyISAM就不可以了。
2）MyISAM适合查询以及插入为主的应用，InnoDB适合频繁修改以及涉及到安全性较高的应用
3）InnoDB支持外键，MyISAM不支持
4）从MySQL5.5.5以后，InnoDB是默认引擎
5）InnoDB不支持FULLTEXT类型的索引
6）InnoDB中不保存表的行数，如select count() from table时，InnoDB需要扫描一遍整个表来计算有多少行，但是MyISAM只要简单的读出保存好的行数即可。注意的是，当count()语句包含where条件时MyISAM也需要扫描整个表
7）对于自增长的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中可以和其他字段一起建立联合索引
8）清空整个表时，InnoDB是一行一行的删除，效率非常慢。MyISAM则会重建表
9）InnoDB支持行锁（某些情况下还是锁整表，如 update table set a=1 where user like ‘％lee％’。