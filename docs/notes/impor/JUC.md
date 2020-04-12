

* java 集合类
* 多线程基础 + JUC
* JVM + GC
* 消息中间件





volatile

java虚拟机提供的轻量级同步机制（乞丐版的synchronized）

三大特性：

* 保证可见性
* 不保证原子性
* 禁止指令重排



JMM

JAVA内存模型， 本身是一种抽象的概念， 并不真实存在， 描述的是一组规范或规则， 

* 可见性
* 原子性
* 有序性



JMM关于同步的规定

* 线程解锁前， 



主内存： 

自己的工作内存 ： 各自线程的工作内存

硬盘 < 内存 < cpu

缓存(cache)



volatile的可见性：

```java
package com.js.study;
import java.util.concurrent.TimeUnit;

/**
 *  1. 验证volatile的可见性
 *     1.1 int number = 0, number之前没有加volatile， 没有可见性。
 *         输出结果显示， 自定的的线程， 已经修改成功， myData.number已经变成了60。 但是主线程中while循						环没有跳出， 即主线程中的myData.number一直是0
 *     1.2 volatile int number = 0, number之前加上volatile.
 *         输出显示， 不管是自定义线程还是主线程中的number都已经变成了60。 线程间有了可见性
 */
public class VolatileDemo {
    public static void main(String[] args) {
        MyData myData = new MyData();

        //第一个线程， 新创建的线程， 修改number的值
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t com in");
            try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
            myData.add60();
            System.out.println(Thread.currentThread().getName() + "\t update number value " + myData.number);
        }, "AAA").start();

        //第二个线程， 主线程
        while(myData.number == 0) {
            //持续等待
        }
        System.out.println(Thread.currentThread().getName() + "\t mission is over");
    }
}

class MyData {
    int number = 0;
//    volatile int number = 0;    //加上volatile关键字， 保证了线程间的可见性

    public void add60() {
        this.number = 60;
    }
}
```







不保证原子性

```java
package com.js.study;

/**
 *  2. 验证volatile 不保证 原子性（不保证、不保证、不保证）
 *    2.1 原子性的意思
 *      不可分割， 完整性， 即某个线程正在做某个具体业务时， 中间不可以被加塞或者分割。 需要整体完整， 要么同时成功， 要么同时失败
 *    2.2 输出结果显示， 最终的number 不一定是20000， 也可能是20000， 即不保证原子性
 			2.3 为什么不保证原子性：
 			  	number++字节码分为三步：
 			    * getfield  得到原始值
 			    * iconst1  : 将1入栈 
 			    * iadd     ： number + 1
 			    * putfield :  把累加的值写回
 			  上述4个步骤在线程私有的虚拟机栈中进行， 有可能某个线程在执行到iadd, 还没有putfield， 此时， 另外					的线程执行getfiled并完成计算。 然后第一个线程putfield, 第二个线程也putfield， 第二个线程覆盖了					第一个线程的值， 此时只加了1次。
 */
public class VolatileDemo {
    public static void main(String[] args) {
        MyData myData = new MyData();

        for (int i = 1; i <= 20; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    myData.addOne();
                }
            }, String.valueOf(i)).start();
        }

        //主线程等待上述20个线程计算完成，查看最终结果

        while (Thread.activeCount() > 2) {
            Thread.yield();
        }
        //等待线程只剩两个线程结束， 只剩主线程和gc线程
        System.out.println(Thread.currentThread().getName() + " " + myData.number);
    }
}

class MyData {
    volatile int number = 0;  //已经加上volatile

    public void addOne() {
        this.number++;
    }
}
```





使用 Atomic原子类保证原子性

```java
package com.js.study;

import java.util.concurrent.atomic.AtomicInteger;

/**
 *  3. 使用 AtomicInteger， 代替volatile修饰的number， 代码不变， 保证了原子性
 */
public class VolatileDemo {
    public static void main(String[] args) {
        MyData myData = new MyData();

        for (int i = 1; i <= 20; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    myData.addByAtomic();
                }
            }, String.valueOf(i)).start();
        }

        //主线程等待上述20个线程计算完成，查看最终结果

        while (Thread.activeCount() > 2) {
            Thread.yield();
        }
        //等待线程只剩两个线程结束， 只剩主线程和gc线程
        System.out.println(Thread.currentThread().getName() + " " + myData.atomicInteger);
    }
}

class MyData {

    AtomicInteger atomicInteger = new AtomicInteger();
    public void addByAtomic() {
        atomicInteger.getAndIncrement();
    }
}
```



有序性(volatile禁止指令重排序)

计算机在执行程序时， 为了提高性能， 编译器和处理器经常会对指令做重排， 一般分为三种：

源代码 --> 编译器优化的重排 --> 指令并行的重排 -->内存系统的重排 -->最终执行的指令



单线程里面会确保程序最终执行结果和代码顺序执行的结果一致

处理器在进行重排序的时候会考虑数据的  **依赖性** 

多线程环境中线程交替执行， 由于编译器优化重排的存在， 两个变量使用的变量能否保证一致性是无法确定的

```java
int x = 11;   //语句1
int y = 12;   //语句2
x = x + 5;    //语句3
y = x * x;    //语句4

/**
	语句执行顺序， 可以是  1 2 3 4； 也可以是 2134   ， 也可以是 1324。由于数据依赖性的存在，语句4 不可以在其他之前
*/



//------------

int x, y, a, b = 0;
//线程1 中
x = a;
b = 1;
  
//线程2	中
y = b;
a = 2;

//在多线程中， 有可能执行的顺序是   a = 2; x = a; b = 1; y = b 导致 x = 2, y = 1;而实际我们需要的效果是 x = 0; y = 0;



//-------
class Resource {
  int a = 0;
  int flag= false;
  
  public void method1() {
    a = 1;
    flag = true;
  }
  
  public void method2() {
    if(flag == true) {
      a = a + 5;
      sout(a)
    }
  }
}

// 多线程中， 两个线程调用两个方法。 我们预想的过程是  先执行 方法1中的a = 1; 然后执行flag = true; 然后执行方法2中的 判断flag, a + 5的操作。 最终得到 a = 6;
//但是， 由于指令重排， 可能会产生这种顺序  flag =true 先执行， 然后进入方法2， 判断为true, 执行 a + 5, 得到结果 a = 5
```



volatile实现**禁止指令重排优化，**从而避免多线程环境下程序出现乱序执行的现象**。**

内存屏障 : 又称内存栅栏，是一个CPU指令，它的作用有两个：

一是保证特定操作的执行顺序

二是保证某些变量的内存可见性（利用该特性实现volatile的内存可见性）



由于编译器和处理器都能执行指令重排优化。如果在指令间插入一条Memory Barrier则告诉编译器和CPU，不管什么指令都不能和这条Memory Barrier指令重新排序，也就是说**通过插入内存屏障禁止在内存屏障前后的指令执行重排序优化**。内存屏障另外一个作用是强制刷出各种CPU的缓存数据，因此任何CPU上的线程都能读取到这些数据的最新版本。





单例模式中

```java
package com.js.study;

public class SingleTonDemo {

    private static SingleTonDemo instance = null;

    private SingleTonDemo() {
        System.out.println("构造方法， 构造一个实例");
    }

    public static SingleTonDemo getInstance() {
        if(instance == null) {
            instance = new SingleTonDemo();
        }
        return instance;
    }


    public static void main(String[] args) {
        
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                SingleTonDemo.getInstance();
            }, String.valueOf(i)).start();
        }

    }
}


/*
	* 上述输出中， 构造函数中的语句可能不止输出一次， 存在问题。 如果给 getInstance()中加 synchronized， 可以解决， 但是加锁 太重
*/
```



上述方式的一种解决方式是两次判断，采用双端检锁机制(DCL)，  在第二次判断中加锁， 相比于在函数上加锁， 更轻

```java
package com.js.study;

public class SingleTonDemo {

    private static SingleTonDemo instance = null;
		//private static volatile SingleTonDemo instance = null;
    private SingleTonDemo() {
        System.out.println("构造方法， 构造一个实例");
    }

    public static SingleTonDemo getInstance() {
        if(instance == null) {
            synchronized(SingleTonDemo.class) {  //注意此处， 静态方法， 没有this对象， 用类的Class对象来进行加锁
                if(instance == null) {
                    instance = new SingleTonDemo();
                }
            }
        }
        return instance;
    }

    public static void main(String[] args) {

        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                SingleTonDemo.getInstance();
            }, String.valueOf(i)).start();
        }

    }
}

```



双端检锁机制不一定安全， 原因是有指令重排序的存在

在某一个线程执行到第一次检测， 读取到的instance不为null的时候， instance的引用对象可能没有完成初始化。原因是：

instance = new SingletonDemo(); 可以分为以下三个步骤：

memory = allocate()  //分配对象内存空间

instance(memory) //初始化对象

instance = memory // 设置instance指向刚分配的内存地址， 此时instance 不为null。

步骤2和3不存在数据依赖关系， 可能执行的步骤是1  3 2 ， 如果是单线程， 执行结果不会受到影响， 此时优化是允许的。 但是如果是多线程， 一个线程先执行了步骤3， 还没有执行步骤2. 另一个线程进入， 读取到instance不为null， 直接拿到instance， 虽然instance不为null， 但是指向的地址内容为空。

因此， 需要在 `private static SingleTonDemo instance = null` 中加上 volatile关键字修饰



## 2. CAS

CAS : compare-And-Swap, 比较并交换.

写入到主内存时， 先判断和设想的版本是否一致， 是的话， 刷新到主内存。

```java
package com.js.study;

import java.util.concurrent.atomic.AtomicInteger;

public class CASDemo {

    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(4);
        System.out.println(atomicInteger.compareAndSet(4, 2020) + "   value is " + atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(4, 2019) + "   value is " + atomicInteger.get());

    }
}

//输出为  true 2020    false 2020

```



Unsafe类

是cas的核心类， 处于 `rt.jar包中的sun.mics包中` ， unsafe中的方法都是native方法。 由于java无法访问底层系统， 需要native方法来访问。 unsafe中的方法操作可以像c的指针一样直接操作内存



CAS是一条cpu并发原语， 执行原语必须是连续的， 在执行的时候不允许被中断， 即CAS不会造成数据不一致， 线程安全。



这是idea中查看到的unsafe中的getAndInt的代码， val1是当前对象，即atomicinteger这个对象， 在主内存中， var2是数据的指针（内存偏移量），var4是要加的值。该方法中， 用一个do while循环，直到和预期中的值一致， 才跳出循环。 保证了数据的原子性。

```java
    public final int getAndAdd(int delta) {
        return unsafe.getAndAddInt(this, valueOffset, delta);
    }
//上面的代码是 AtomicInteger中的getandadd方法源码

//下面的代码是unsafe中的getAndAddInt方法源码
		public final int getAndAddInt(Object var1, long var2, int var4) {
        int var5;
        do {
            var5 = this.getIntVolatile(var1, var2);
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

        return var5;
    }
```

unsafe中的compareAndSwapInt是个本地方法， 该方法的实现位于unsafe.cpp中， 底层是汇编语言，里面有一些汇编相关的语句保证原子性

unsafe + cas(自旋) 



CAS缺点

* 循环时间长， 开销大。 如果cas中while中为false， 会一直进行尝试， 长时间不成功， 导致太大的开销
* 只能保证一个共享变量的原子性
* 导致 ABA问题 * 



ABA问题

cas算法实现中重要前提是取出内存中的数据并比较替换。然后在这个时间差中，可能会导致数据的变化。可能被其他线程修改很多次， 但是最终与该线程取出的时候数据一致， 该线程会认为数据并没有被修改过。尽管该线程操作会成功， 但是不代表该过程没有问题。



原子引用

AtomicReference， 和原子整型差不多， 区别在于此处是引用

```java
package com.js.study;

import java.util.concurrent.atomic.AtomicReference;


public class VolatileDemo {
    public static void main(String[] args) {
        AtomicReference<User> atomicReference = new AtomicReference<>();
        User user1 = new User("zhangsan", 13);
        User user2 = new User("lisi", 43);

        atomicReference.set(user1);

        System.out.println(atomicReference.compareAndSet(user1,user2) + " " + atomicReference.get());
        System.out.println(atomicReference.compareAndSet(user1,user2) + " " + atomicReference.get());

    }
}

class User {
    String name;
    int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

//和
```



解决 ABA问题： 原子引用 + 版本号控制（类似于时间戳）

被修改一次， 版本号+1， 判断的时候， 判断版本号是否是一致的， 一致表示没有变化过（阿里巴巴手册中数据库的构建中， 需要多一个字段， 就是版本号， 思想差不多？？）



AtomicStampedReference<V>

```java
package com.js.study;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.atomic.AtomicStampedReference;


public class VolatileDemo {
    public static void main(String[] args) {

        AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(100, 1);

        new Thread(() -> {
            int stamp = atomicStampedReference.getStamp();
            System.out.println("A线程中第一次版本号为 " + stamp);
            try {
                TimeUnit.SECONDS.sleep(2);
            }catch (Exception e) {
                System.out.println(e);
            }
            atomicStampedReference.compareAndSet(100, 101, stamp, stamp + 1);
            atomicStampedReference.compareAndSet(101, 100, stamp + 1, stamp + 2);
            System.out.println("修改两次后版本号变为 " + atomicStampedReference.getStamp());
        }, "A").start();

        new Thread(() -> {
            int stamp = atomicStampedReference.getStamp();
            System.out.println("B线程中第一次版本号为 " + stamp);
            try {
                TimeUnit.SECONDS.sleep(4);
            }catch (Exception e) {
                System.out.println(e);
            }

            boolean flag = atomicStampedReference.compareAndSet(100, 200, stamp, stamp + 1);
            System.out.println("the flag is " + flag);
        },"B").start();
    }
}


/*
输出为： 
A线程中第一次版本号为 1
B线程中第一次版本号为 1
修改两次后版本号变为 3
the flag is false
*/
```





## 锁

公平锁， 非公平锁

公平锁： 多个线程按照申请锁的顺序来获取锁， 先来后到

非公平锁： 多个线程获取锁的顺序并不是按照申请锁的顺序， 有可能后申请的比先申请的线程优先获取锁。在高并发下， 可能造成优先级反转或者饥饿现象。如果新的线程尝试优先获取失败， 再按照公平锁的方式



非公平锁的优点是吞吐量比公平锁大

```java
Lock lock = new ReentranLock() //默认是非公平锁
Lock lock = new ReentranLock(true) //设置公平锁
  
//  synchronized 也是一种非公平锁
```





可重入锁（递归锁）

同一个线程中，外层获得锁之后， 内层仍然可以获取该锁， 即线程可以进入任何一个它已经拥有的锁所同步着的代码块。

可重入锁的最大作用是避免死锁

`ReentranLock` 和 `synchronized` 都是可重入锁。

```java
public synchronized void method() {
  method2();
}
public synchronized void method2() {
  //to do
}
```

