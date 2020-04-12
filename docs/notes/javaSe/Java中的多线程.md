# Java中的多线程

## 1. 进程与线程

进程：一个任务称为一个进程， 比如浏览器是一个进程， 一个聊天软件是一个进程。

线程：某些进程内部会同时执行多个字任务。 子任务就是线程。

进程与线程是包含关系， 一个进程包含一个或者多个线程。 

线程是操作系统资源调度的最小单位。 如何调度线程完全由操作系统决定， 程序不能决定线程什么时候执行和执行多长时间。



一个应用程序， 是可以有多个进程的，实现多任务的方式有三种：

* 多进程模式（每个进程只有一个线程）
* 多线程模式（只有一个进程， 但是包含多个线程）
* 多进程 + 多线程



多进程与多线程比较：

* 创建进程的开销比创建线程大
* 进程间的通信比线程间通信慢， 因为线程通信是读写同一个变量
* 多进程稳定性比多线程高， 一个进程崩溃不会影响其他进程。



一个java程序是一个JVM进程， JVM用一个主线程执行 `main()` 方法， 在	`main()`  方法内部， 可以启动多个线程。 此外， JVM还有负责gc的其他线程等。



## 2. 多线程基础

创建新线程只需要实例化一个  `Thread` 对象， 然后调用 `start()`  方法

但是， 上述方法只是启动线程， 调用方法后并没有任何结果， 因为`Thread`类中的 `start()` 实际上是调用内部的 `run()`	方法， 默认的方法内部不执行任何语句。 因此需要重写该方法。 方式有如下几种：

* 继承 `Thread` 类， 重写 `run()` 方法

  ```java
  public class Main {
    public static void main(String[] args) {
      Thread t = new MyThread();
      t.start();   //启动新线程
    }
  }
  class MyThread extends Thread {
    @override
    public void run() {
      //TODO
    }
  }
  ```

  

* 实现 `Runnable` 接口， 创建线程时， 将 `Runnable` 实例作为参数传入

  ```java
  public class Main {
    public static void main(String[] args) {
      Thread t = new MyThread(new MyRunnable());
      t.start();   //启动新线程
    }
  }
  
  class MyRunnable implements Runnable {
    @override
    public void run() {
      //TODO
    }
  }
  ```

  java8中利用lambda表达式可以简写上面的代码：

  ```java
  public class Main {
    public static void main(String[] args) {
      Thread t = new MyThread(() -> {
        //TODO
      });
      t.start();   //启动新线程
    }
  }
  ```

  

主线程和新线程是同时执行的， 没有先后之分。但是可以设置线程的优先级。

```java
Thread setPriority(int n) // 1-10, 默认值5，
```

优先级高的线程被操作系统调度的优先级高， 但是优先级只是意味着更频繁， 即概率更大， 并不能保证高优先级的线程一定会先执行。



***线程状态***

线程有6种状态， 分别为：

* `New` ： 创建新的线程， 尚未执行。 `Thread t = new Thread(...)`

* `Runnable` ： 运行总的线程， 只在执行 `run()` 方法
* `Blocked` ： 运行中的线程， 因为某次操作被阻塞
* `Waiting` ： 运行中的线程， 因为某些操作在等待中
* `Timed Waiting` ： 运行中的线程， 因为执行了 `sleep()` 正在睡眠等待
* `Terminated` : 线程已经终止， 因为`run()` 方法执行结束

线程终止有三种情况：

* 正常终止， `run()` 方法正常执行到 `return` 语句
* 意外终止 , `run()` 方法因为没有捕获的异常导致线程终止
* 调用 `stop()` 方法强制终止（尽量不要使用）



***线程等待***

一个线程可以等待另一个线程结束之后再继续执行， 使用方法为：`join()` ， 比如主线程等待一个新线程结束再执行代码如下：

```java
public class Main {
  public static void main(String[] args) {
    Thread t = new MyThread(() -> {
      //TODO
    });
    t.start();   //启动新线程
    t.join(); // 主线程等待t线程执行结束完毕
    // TODO of main thread
    //...  主线程剩下的代码
  }
}
```



***中断线程***

中断线程是其他线程给该线程发一个中断信号， 该线程收到信号后结束执行 `run()`方法， 立刻结束该线程。（比如下载速度很慢， 用户点击取消下载， 就类似于中断线程）。

使用： 对目标线程调用 `interrupt()` 方法。 代码示例：

```java
public class Main {
  public static void main(String[] args) {
    Thread t = new MyThread();
    t.start();   //启动新线程
    t.interrupt(); //主线程中断 t线程
    t.join();  //主线程等待t线程执行结束
    
    // left TODO of main thread
  }
}

class MyThread extends Thread {
  @override
  public void run() {
    int n = 0;
    while(!isInterrupted()) { // 循环检测是否被中断
      n += 1;
      sout(n);
    }
  }
}
```

另一种结束线程的方式是设置标识位， 当该线程读取到的标志位发生变化， 则结束：

```java
public class Main {
  public static void main(String[] args) {
    Thread t = new MyThread();
    t.start();   //启动新线程
    Thread.sleep(1000);
    t.running = false;  // 设置标志位位false
  }
}

class MyThread extends Thread {
  public volatile boolean running = true;
  @override
  public void run() {
    int n = 0;
    while(running == true) {
      n += 1;
      sout(n);
    }
  }
}
```

上述标志位需要使用 volatile 关键词标记， 保证每个线程都能读到更新后的变量值（JVM底层知识）



***守护线程***

如果有一个线程没有退出， 那么JVM进程就不退出， 因此必须保证所有的线程都能及时退出。但是有些线程的目的就是无限循环， 比如一个定时触发任务的线程， 如果不结束， JVM就不能结束。 但是希望其他线程结束后， 就结束JVM， 此时， 可以将该线程设置为守护线程。 JVM退出时， 不关心守护线程是否结束。

只需要在调用start方法之前， 调用 `setDaemon(true)` 方法即可将线程标记为守护线程。

***守护线程最典型的应用就是 GC (垃圾回收器)。***



## 3. 线程之间的同步

多线程对共享变量进行读写时， 必须保证指令以原子方式执行， 即某一个线程执行时， 其他线程必须等待。

通过 `synchronized` 关键字进行加锁。

* 方式一， 在执行语句块中使用`synchronized(object)`进行加锁。操作同一个共享变量的话， 多个线程中 `synchronized(object)` 中传入的 `object` 必须是同一个对象。 很常用的方式是， 将语句块定义在一个函数中， object就可以使用 this 来保证同一个对象， 多个线程调用该方法即可。
* 方式2 ： 在方法定义的时候， 不再语句块中， 而在定义时加上 `synchronized` 关键字，实际上就是获取 this 的锁。 如果定义一个静态方法， 因为静态方法是归属于类， 并没有this对象， 那么锁就是该类的Class对象， 因为JVM内部加载每一个类的时候会创建一个Class对象(参考Java中的反射)。



***不需要synchronized的操作***

Java规范中， 本身就是原子操作的有如下：

* 基本类型(long和double除外)的赋值， 比如 `int i = m`
* 引用类型赋值， 例如： `List<String> list = anotherList`

需要注意的是， 但是多行语句赋值操作， 则必须手动保证是同步操作， 上述规定中， 只适用于单行语句赋值操纵。



***死锁***

JVM允许同一个线程重复获取同一个锁， 称为可重入锁。

也可以获取一个锁之后， 再获取另一个锁， 此时可能会导致死锁。比如下面的例子：

```java
public void add(int m) {
  synchronized(lockA) {
    this.value += m; //操作共享变量value
    synchronized(lockB) {
      this.another += m; //操作另一个共享变量
    }//释放lockB的锁
  }//释放lockA的锁
}
public void dec(int n) {
  synchronized(lockB) {
    this.anther -= n;
    synchronized(lockA) {
      this.value -= n;
    }
  }
}
```

如果两个线程同时调用两个方法， 则会出现死锁， 过程如下：

- 线程1：进入`add()`，获得`lockA`；
- 线程2：进入`dec()`，获得`lockB`。

随后：

- 线程1：准备获得`lockB`，失败，等待中；
- 线程2：准备获得`lockA`，失败，等待中。

两个线程各自持有不同的锁， 试图获取对方的锁， 双方会无限等待下去。发生死锁， 只能强制结束JVM进程。



***多线程协调***

`wait`和`notify`用于多线程协调运行：

- 在`synchronized`内部可以调用`wait()`使线程进入等待状态；
  - 必须在已获得的锁对象上调用`wait()`方法, 比如如果锁是this,则调用`this.wait()`；
- 在`synchronized`内部可以调用`notify()`或`notifyAll()`唤醒其他等待线程；
- 必须在已获得的锁对象上调用`notify()`或`notifyAll()`方法；
- 已唤醒的线程还需要重新获得锁后才能继续执行。



## 4. 多线程高级

***ReentrantLock***

java 5开始引入了高级的处理并发的 `java.util.concurrent`包， 提供了大量高级的并发功能。

`java.util.concurrent.locks` 包中提供的 `ReentrantLock` 可以替代 `synchronized` 关键字进行加锁。但是需要注意的是， `synchronized`关键字是Java语言层面的语法， 并不需要考虑异常的发生，在语句块最后会自动释放锁资源，  但是 `ReentrantLock` 是封装好的类， 使用 的时候需要先获取锁， 然后在 `finally` 正确释放锁。

使用：

```java
public class Counter {
  private finally Lock lock = new ReentrantLock();
  private int count;
  
  public void add(int n) {
    lock.lock();
    try {
      count += n;
    }finally {
      lock.unlock();
    }
  }
}
```

`ReentrantLock` 还可以尝试获取锁：

```java
if(lock.tryLock(1, TimeUnit.SECONDS)) {
  try {
    //TODO
  }finally {
    lock.unlock();
  }
}
```

尝试获取锁的时候， 最多等待1秒中， 如果没有获取到， 则返回false， 不执行语句块内的内容。



***Condition***

`Condition`可以实现 `wait` 和 `notify` 的功能。`Condition`对象必须从 `Lock`对象中获取， 掩饰如下：

```java
class TaskQueue {
  private final Lock lock = new ReentrantLock();
  private final Condition condition = lock.newCondition();
  private Queue<String> queue = new LinkedList<>();
  
  public void addTask(String s) {
    lock.lock();
    try {
      queue.add(s);
      condition.signalAll();
    }finally {
      lock.unlock();
    }
  }
  
  public String getTask() {
    lock.lock();
    try {
      while(queue.isEmpty) {
        condition.await();
      }
      return queue.remove();
    }finally {
      lock.unlock;
    }
  }
}
```

和 `ReentrantLock`中的`tryLock`类似， `Condition`中也可以指定等待时间：

```java
if(condition.await(1, TimeUnit.SECOND)) {
  //
}else {
  //指定时间没有被其他线程唤醒
}
```



***ReadWriteLock***

允许多个线程同时读取， 但是不允许多个线程同时写。适用条件是有大量线程读取， 少量线程写入， 即修改变量不频繁。

用法示例如下：

```java
public class Counter {
  private final ReadWriteLock rwlock = new ReentrantReadWriteLock();
  private final Lock rlock = rwlock.readLock();
  private final Lock wlock = rwlock.writeLock();
  private int[] counts = new int[10];
  
  public void inc(int index) {
    wlock.lock(); // 写锁
    try {
      count[index] += 1;
    }finally {
      wlock.unlock();
    }
  }
  public int[] get() {
    rlock.lock();
    try {
      return Arrays.copyOf(counts, counts.length);
    }finally {
      rlock.unlock();
    }
  }
}
```



***StampedLock***

`ReadWriteLock` 是一个悲观锁， 读的过程中不允许写， `StampedLock` 是一个乐观锁， 允许读的过程中进行写入（乐观锁是读的过程中大概率不会写入， 采取乐观的方式。如果读的过程中被写了， 读到的内容会发生错误， 因此需要在读的时候再次判断读锁后是否有写锁发生

示例如下：

```java
public class Point {
    private final StampedLock stampedLock = new StampedLock();

    private double x;
    private double y;

    public void move(double deltaX, double deltaY) {
        long stamp = stampedLock.writeLock(); // 获取写锁
        try {
            x += deltaX;
            y += deltaY;
        } finally {
            stampedLock.unlockWrite(stamp); // 释放写锁
        }
    }

    public double distanceFromOrigin() {
        long stamp = stampedLock.tryOptimisticRead(); // 获得一个乐观读锁
        // 注意下面两行代码不是原子操作
        // 假设x,y = (100,200)
        double currentX = x;
        // 此处已读取到x=100，但x,y可能被写线程修改为(300,400)
        double currentY = y;
        // 此处已读取到y，如果没有写入，读取是正确的(100,200)
        // 如果有写入，读取是错误的(100,400)
        if (!stampedLock.validate(stamp)) { // 检查乐观读锁后是否有其他写锁发生
            stamp = stampedLock.readLock(); // 获取一个悲观读锁
            try {
                currentX = x;
                currentY = y;
            } finally {
                stampedLock.unlockRead(stamp); // 释放悲观读锁
            }
        }
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
}
```

上述代码有两次的保障， 第一次读取， 判断是否有写锁， 如果是， 再次读取。写入的概率不高， 绝大部分第一次读取就可以获取到数据。



***Concurrent集合***

`java.util.concurrent`包提供了并发的集合类， 帮我们封装好了并发， 使用的时候和正常结合一样。最常用的 `ConcurrentHashMap`

```java
Map<String, String> map = new ConcurrentHashMap<>();
map.put("a", '1');
map.get("a");
//.......
```



***Atomic***

`java.util.concurrent.atomic` 包提供了原子操作的封装类， 以 `AtomicInteger`为例， 主要操作有：

* 增加并返回新值 ： `int addAndGet(int delta)`

- 加1后返回新值：`int incrementAndGet()`
- 获取当前值：`int get()`
- 用CAS方式设置：`int compareAndSet(int expect, int update)`

Atomic类是通过无锁（lock-free）的方式实现的线程安全（thread-safe）访问。它的主要原理是利用了CAS：Compare and Set。



## 5. 线程池

java标准库提供了`ExecutorService`接口表示线程池，它的典型用法如下：

```java
// 创建固定大小的线程池:
ExecutorService executor = Executors.newFixedThreadPool(3);
// 提交任务:
executor.submit(task1);
executor.submit(task2);
executor.submit(task3);
executor.submit(task4);
executor.submit(task5);
```

因为`ExecutorService`只是接口，Java标准库提供的几个常用实现类有：

- FixedThreadPool：线程数固定的线程池；
- CachedThreadPool：线程数根据任务动态调整的线程池；
- SingleThreadExecutor：仅单线程执行的线程池。

使用注意：

- 必须调用`shutdown()`关闭`ExecutorService`；
- `ScheduledThreadPool`可以定期调度多个任务





***Future***

在执行多个任务的时候，使用Java标准库提供的线程池是非常方便的。我们提交的任务只需要实现`Runnable`接口，就可以让线程池去执行：

```
class Task implements Runnable {
    public String result;

    public void run() {
        this.result = longTimeCalculation(); 
    }
}
```

`Runnable`接口有个问题，它的方法没有返回值。如果任务需要一个返回结果，那么只能保存到变量，还要提供额外的方法读取，非常不便。所以，Java标准库还提供了一个`Callable`接口，和`Runnable`接口比，它多了一个返回值：

```
class Task implements Callable<String> {
    public String call() throws Exception {
        return longTimeCalculation(); 
    }
}
```

并且`Callable`接口是一个泛型接口，可以返回指定类型的结果。

获得异步执行的结果:

`ExecutorService.submit()`方法，它返回了一个`Future`类型，一个`Future`类型的实例代表一个未来能获取结果的对象：

```
ExecutorService executor = Executors.newFixedThreadPool(4); 
// 定义任务:
Callable<String> task = new Task();
// 提交任务并获得Future:
Future<String> future = executor.submit(task);
// 从Future获取异步执行返回的结果:
String result = future.get(); // 可能阻塞
```

当我们提交一个`Callable`任务后，我们会同时获得一个`Future`对象，然后，我们在主线程某个时刻调用`Future`对象的`get()`方法，就可以获得异步执行的结果。在调用`get()`时，如果异步任务已经完成，我们就直接获得结果。如果异步任务还没有完成，那么`get()`会阻塞，直到任务完成后才返回结果。

一个`Future`接口表示一个未来可能会返回的结果，它定义的方法有：

- `get()`：获取结果（可能会等待）
- `get(long timeout, TimeUnit unit)`：获取结果，但只等待指定的时间；
- `cancel(boolean mayInterruptIfRunning)`：取消当前任务；
- `isDone()`：判断任务是否已完成。



***CompletableFuture***

使用`Future`获得异步执行结果时，要么调用阻塞方法`get()`，要么轮询看`isDone()`是否为`true`，这两种方法都不是很好，因为主线程也会被迫等待。

从Java 8开始引入了`CompletableFuture`，它针对`Future`做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。

有点类似于javascript中的ajax异步请求

`CompletableFuture`可以指定异步处理流程：

- `thenAccept()`处理正常结果；
- `exceptional()`处理异常结果；
- `thenApplyAsync()`用于串行化另一个`CompletableFuture`；
- `anyOf()`和`allOf()`用于并行化多个`CompletableFuture`。



***ForkJoin***

Java 7开始引入了一种新的Fork/Join线程池，它可以执行一种特殊的任务：把一个大任务拆成多个小任务并行执行。

Fork/Join是一种基于“分治”的算法：通过分解任务，并行执行，最后合并结果得到最终结果。

`ForkJoinPool`线程池可以把一个大任务分拆成小任务并行执行，任务类必须继承自`RecursiveTask`或`RecursiveAction`。

使用Fork/Join模式可以进行并行计算以提高效率。



***ThreadLocal***

`ThreadLocal`表示线程的“局部变量”，它确保每个线程的`ThreadLocal`变量都是各自独立的；

`ThreadLocal`适合在一个线程的处理流程中保持上下文（避免了同一参数在所有方法中传递）；

使用`ThreadLocal`要用`try ... finally`结构，并在`finally`中清除。



## 6. 备注

笔记中线程池的内容，诸如 ***CompletableFuture*** ，***ForkJoin***， ***ThreadLocal*** 等内容没有过多阐述， 用到的时候， 可以仔细的查询和学习。