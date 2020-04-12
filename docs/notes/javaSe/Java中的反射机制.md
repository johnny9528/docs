# Java中的反射

## 1 什么是反射

class在jvm的执行中是动态加载的， 即jvm在第一次读取到一种class类型时， 将其加载进内存。每加载一种class, jvm会为其创建一个Class(注意是大写， Class类型是一个名字为Class的class类， 和普通的Student类一样的类)的实例。Class类如下：

```java
public final class Class {
  private Class(){}   //构造方法， 私有的， 只有jvm能够创建Class实例对象
}
```

当jvm加载一个类的时候， 以String为例， jvm先读取String.class文件到内存中， 然后， 为String创建一个Class类实例并与该类关联起来, 即

```java
Class clazz = new Class(String);
```

因此， JVM中的每一个Class实例对象都关联了一个类或者接口，并且， 这些实例包含了对应class或者interface的所有信息， 包括类名，包名， 父类， 实现的接口， 所有方法， 所有字段等信息。

需要注意的是， JVM为每一个class只会创建一个Class实例， 类似于Spring里面的单例模式，创建单例bean。 所以，程序中比较多个相同class的Class对象时， 可以用“==”判断， 同时， 也可以用“equals”判断。

***因此， 通过这个Class实例对象来获取class的信息的方式就叫反射.***



## 2 获取Class实例对象的三种方法(以String为例)

* 使用class的静态变量class

  ```java
  Class clazz = String.class;
  ```

* 通过class的实例对象获取

  ```java
  String s = "123";
  Class clazz = s.getClass();
  ```

* 通过class的完整路径获取

  ```java
  Class clazz = Class.forName("java.lang.String")
  ```

正如第一节中所说， 同一个类在JVM中创建的Class实例对象是单例的， 因此上述三种方法得到的clazz是同一个对象。

***JVM总是动态加载class, 可以在运行期间根据条件来控制加载clas***



## 3 访问字段

```java
* Field getField(name)  // 根据字段名获取某个public的field(包括父类)   注意是public
* Field getDeclaredField(name)  //获取该类的所有field， 不包括父类   注意没有规定是public 
* Field[ ] getFields()  //获取所有的public的field(包括父类)
* Field[ ] getDeclaredFields()  //获取当前类的所有field， 不包括父类。

//第一种与第二种的区别主要有两点， 第一是第一类包含父类的字段，第二类不包含；第二是第一类只能获取public属性， 第二类没有限制
```



***一个Field对象包含了一个字段(属性)的所有信息***

```java
* String getName() //返回字段的名称， 比如“name”
* Class getType()  // 返回字段类型， 是一个Class实例， 比如 String,class;
* int getModifiers() // 返回字段的修饰符， 返回int , 不同bit代表不同的含义
```



***通过Field对象获取字段的值***

 先获取 **Class** 实例， 然后获取 **Field** 实例对象 f， 之后， 根据 **f.get(object)** 获取指定class对象的字段的值； 
同理， 设置某个属性的值， 根据 **f.set(object, value)** 来设置或者改变属性。 
但是， 需要注意的是， 如果该字段属性的访问权限是 ***private***  , 则不可以通过上述方式获取和设置字段。此时， 可以通过先设置字段的访问权限， 即加一句 ： **f.setAccessible(true)** 



## 4 通过Class实例对象来调用方法(函数)

```java
Method getMethod(name, Class...) //获取某个public的Method(包括父类)， Class...为可变参数,表示原本方法中的参数类型
Method getDeclaredMethod(name, Class...) //获取当前类的某个Method， 不包括父类
Method[ ] getMethods( ) //获取所有public的Method(包括父类)
Method[ ] getDeclaredMethods( ) //获取当前类的所有Method ，不包括父类
```



***一个Method对象包含了该方法的所有信息***

```java
String getName( ) //返回方法名称， 比如“getData”;
Class getReturnType //返回方法的返回值类型， 为一个Class 实例， 比如 String.class;
Class[ ] getParameterTypes( ) //返回方法的参数类型， 是一个 Class 数组， 比如 {String.class, int.class, Student.class}
int getModifiers( )  // 返回方法的修饰符， 是一个int, 不用的bit代表不同的含义
```



***调用方法***

```java
Object invoke(object, para...) //invoke 中第一个参数为class的实例对象， 后面为可变参数Class类型， 传入方法本身需要的参数类型
```

需要注意的是，此处的可变参数必须与之前获取Method时的可变参数对应， 否则程序错误， 当方法重载的时候， 需要特别注意。 
**以String类中的substring为例**

```java
String s = "  ";
String s1 = "abcdefg"
Class clazz = s.getClass();
Method m = clazz.getMethod("substring", int.class, int.class);
String ans = (String) m.invoke(s1, 0, 3);
```

***如果是静态方法， invoke中的第一个参数， 为null*** 
***与字段类似， 如果调用非public 的方法， 需要在调用invoke之前先设置权限， m.setAccessible(true)***



## 5 调用构造函数

* 通过Class中的 **newInstance()** 方法

  ```java
  Class clazz = Student.class;
  Student stu = clazz.newInstance();
  ```

  上述方法只能通过调用无参构造方法来构造对象， 具有一定的局限性，如果是有参构造，或者构造函数权限不是public 该方法则不能使用

* 通过Class实例调用 ***Constructor***对象

  ```java
  Constructor Constructor getConstructor(Class...) //获取某个public的constructor
  Constructor getDeclaredConstructor(Class...) //获取某个Constructor
  Constructor[] getConstructors() //获取所有public的Constructor
  Constructor[] getDeclaredConstructors() //获取所有的Constructor
  
  //Constructor与Field和Method不同， 构造方法总是当前类定义的构造方法， 与父类无关，也不存在多态问题
  ```

  ***同理， 访问非public构造函数时， 需要设置c.setAccessible(true)***
  ***需要说明的是， 这三种设置非public的访问权限都不一定会成功， 某些特殊情况会导致失败***

## 6 获取父类对象或者接口

```java
Class getSuperclass() //获取父类
Class[] getInterfaces //获取接口， 返回一个数组
```



## 7 动态代理

不编写实现类， 直接在运行期间创建某个interface的实例， 采用动态代理 
（Springboot整和mabatis或者mybatisPlus中， 在注解模式下， 在mybatis接口中之前添加@Mapper注解后可以直接写sql语句， 因此并没有任何接口的实现类。此处就是使用了动态代理， 但是idea之类的编辑器在预编译的时候， 检查到并没有对接口进行实现， 但是却在ioc容器中自动注入了该接口构造的对象， 因此ide编辑器会报错。 原因就在于此， 并不是程序本身有错误， 而是该过程采用了动态代理， 直接在运行期间创建inteface的实例，运行没有错误， idea预编译认为其错误） 
过程如下：

1. 定义一个InvocationHandler实例， 负责对实现接口的方法调用
2. 通过Proxy.newProxyInstance()创建interface实例， 需要3个参数 
   a. 使用的ClassLoader, 通常就是接口类的ClassLoader 
   b. 需要实现的接口数组， 至少需要传入一个接口 
   c. 用来处理接口方法调用的InvocationHandler实例
3. 将返回的Object强转为接口

```java
public class Main {
    public static void main(String[] args) {
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method);
                if (method.getName().equals("morning")) {
                    System.out.println("Good morning, " + args[0]);
                }
                return null;
            }
        };
        Hello hello = (Hello) Proxy.newProxyInstance(
            Hello.class.getClassLoader(), // 传入ClassLoader
            new Class[] { Hello.class }, // 传入要实现的接口
            handler); // 传入处理调用方法的InvocationHandler
        hello.morning("Bob");
    }
}
```

