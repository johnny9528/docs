# Java8——函数式编程、Stream

Java8开始引入的函数式编程和 `Stream` 流中的用法， 和javascript语法中的 es6的用法及其相似（大致原理都差不多）， 因此也可以参考 es6的用法。

## 1. Lambda表达式

***FunctionalInterface***  

只定义了单方法的接口就是 `FunctionalInterface` ， 使用该注解进行标记（和 `@override` 注解一样， 不是必须的， 但是尽量加上）

```java
@FunctionalInterface
public interface Callable<V> {
  V call() throws Exception;
}
```

上述接口是多线程中的 `Callable` 接口（回顾：创建一个新线程有三种方式：自定义类实现 `Extends` 类， 传入一个实现 `Runnable`接口的对象参数， 传入一个实现 `Callable`接口的对象参数。 `Runnable` 和 `Callable`的区别在于前者的`run()`方法没有返回值， 后者的`call()` 方法有返回值， 并且可以使用范型）



再比如 `Comparator`接口：

```java
@FunctionalInterface
public interface Comparator<T> {
  int compare(T o1, T o2);
  
  boolean equals(Object obj);
  default Comparator<T> reversed() {
    return Collections.reverseOrder(this);
  }
  ......
}
```

上述接口虽然有很多方法， 但是只有一个抽象方法 `compare()` ， 其他方法都是 default方法或者 `static` 方法。 特殊的一个是 `equals` 方法是 `Object` 定义的方法， 不算在接口方法内， 因此， `Comparator` 接口也是一个单方法的接口。



因此， 比如要实现一个排序功能， 之前使用的方式是传入一个匿名类对象， 现在则可以使用 `lambda` 表达式。

```java
//使用 匿名类
List<String> list = Arrays.asList("abc", "GFD", "kdf");
Collections.sort(array, new Comparator<String>(){
  @override
  public int compare(String s1, String s2) {
    return s2.compareTo(s1); //逆序排列， String本身有compareTo方法
  }
});    //注意分号， 这只是一个完整的语句。

//使用 lambda表达死
List<String> list = Arrays.asList("abc", "GFD", "kdf");
Collections.sort(list, (s1, s2) -> s2.compareTo(s1));
```



## 2. 方法引用

除了上述的 `Lambda` 表达式， 可以不用编写实现类， 简化了代码。还可以直接传入方法的引用， 更加简化代码。比如：

```java
public class Main {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, Main::cmp);
        System.out.println(String.join(", ", array));
    }

    static int cmp(String s1, String s2) {
        return s1.compareTo(s2);
    }
}
```

上述代码在`Arrays.sort()`中直接传入了静态方法`cmp`的引用，用`Main::cmp`表示。

因此，所谓方法引用，是指如果某个方法签名和接口恰好一致，就可以直接传入方法引用。

因为`Comparator`接口定义的方法是`int compare(String, String)`，和静态方法`int cmp(String, String)`相比，除了方法名外，方法参数一致，返回类型相同，因此，我们说两者的方法签名一致，可以直接把方法名作为Lambda表达式传入。



上述方法是传入静态方法， 如果是非静态， 即实例方法， 也是可以的， 但是需要注意一点。比如：

```java
public class Main {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, String::compareTo);
        System.out.println(String.join(", ", array));
    }
}
```

而 String内部是这样定义的

```java
public final class String {
    public int compareTo(String o) {
        ...
    }
}
```

需要注意的是这个方法的签名只有一个参数

实例方法有一个隐含的`this`参数，`String`类的`compareTo()`方法在实际调用的时候，第一个隐含参数总是传入`this`，相当于静态方法：

```
public static int compareTo(this, String o);
```

所以，`String.compareTo()`方法也可作为方法引用传入。



## 3. Stream

`Stream` 实现的是一种惰性计算， 普通的集合类保存的都是一些存储在内存中的实际元素， 但是 `Stream` 并没有实际保存和计算， 而是一种概念上的存储和计算， 定义了一些约束， 真正在使用的时候（诸如打印输出之类的调用）， 才会真正意义上分配内存和计算。

Stream API的特点是：

- Stream API提供了一套新的流式处理的抽象序列；
- Stream API支持函数式编程和链式操作；
- Stream可以表示无限序列，并且大多数情况下是惰性求值的。



***创建Stream***

* `Stream.of()` :是一种静态方法， 直接传入可变参数

  ```java
  public class Main {
      public static void main(String[] args) {
          Stream<String> stream = Stream.of("A", "B", "C", "D");
          // forEach()方法相当于内部循环调用，
          // 可传入符合Consumer接口的void accept(T t)的方法引用：
          stream.forEach(System.out::println);
      }
  }
  ```

* 基于数组和集合

  ```java
  public class Main {
      public static void main(String[] args) {
          Stream<String> stream1 = Arrays.stream(new String[] { "A", "B", "C" });
          Stream<String> stream2 = List.of("X", "Y", "Z").stream();
          stream1.forEach(System.out::println);
          stream2.forEach(System.out::println);
      }
  }
  ```

  把数组变成`Stream`使用`Arrays.strem()`方法。对于 `Collection`（`List`、`Set`、`Queue`等），直接调用`stream()`方法就可以获得`Stream`。

  上述创建`Stream`的方法都是把一个现有的序列变为`Stream`，它的元素是固定的。

* 基于 Supplier

  创建`Stream`还可以通过`Stream.generate()`方法，它需要传入一个`Supplier`对象：

  ```
  Stream<String> s = Stream.generate(Supplier<String> sp);
  ```

  基于`Supplier`创建的`Stream`会不断调用`Supplier.get()`方法来不断产生下一个元素，这种`Stream`保存的不是元素，而是算法，它可以用来表示无限序列。

  例如，编写一个能不断生成自然数的`Supplier`，它的代码非常简单，每次调用`get()`方法，就生成下一个自然数：

  ```java
  public class Main {
      public static void main(String[] args) {
          Stream<Integer> natual = Stream.generate(new NatualSupplier());
          // 注意：无限序列必须先变成有限序列再打印:
          natual.limit(20).forEach(System.out::println);
      }
  }
  
  class NatualSupplier implements Supplier<Integer> {
      int n = 0;
      public Integer get() {
          n++;
          return n;
      }
  }
  
  ```

* 其他方式：通过一些api提供的接口， 比如 Files类中的 `lines()` 方法, 比如正则表达式中的某些方式。

  需要注意的是， Java中范型不支持基本类型， 于是第三种方式，所以Java提供了三种基本类型的 `stream`， `IntStream`、`LongStream`和`DoubleStream`这三个， 可以避免使用 `Interger`这样的包装类， 提升效率。

  ```java
  // 将int[]数组变为IntStream:
  IntStream is = Arrays.stream(new int[] { 1, 2, 3 });
  // 将Stream<String>转换为LongStream:
  LongStream ls = List.of("1", "2", "3").stream().mapToLong(Long::parseLong);
  ```



***map的使用***

`Stream.map()` 是一个转换方法， 可以把一个流映射为另一个 流。

```java
Stream<Integer> s = Stream.of(1, 2, 3, 4, 5);
Stream<Integer> s2 = s.map(n -> n * n);

List.of("  Apple ", " pear ", " ORANGE", " BaNaNa ")
                .stream()
                .map(String::trim) // 去空格
                .map(String::toLowerCase) // 变小写
                .forEach(System.out::println); // 打印

```



***filter***

这是另一个转换方法， 执行过滤操作， 不满足条件则过滤掉

```java
IntStream.of(1, 2, 3, 4, 5, 6, 7, 8, 9)
                .filter(n -> n % 2 != 0)
                .forEach(System.out::println);
```



***reduce***

这是一个聚合方法， 可以把一个流中的所有元素聚合起来， 比如完成累加。

```java
        int sum = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9).reduce(0, (acc, n) -> acc + n);
        System.out.println(sum); // 45
```



还是需要注意的是， 上述的操作，转换方法不会触发计算， 但是聚合方法会触发计算。



***Stream输出为集合和数组***

```java
// 输出为数组
List<String> list = List.of("Apple", "Banana", "Orange");
String[] array = list.stream().toArray(String[]::new);

//列表
 Stream<String> stream = Stream.of("Apple", "", null, "Pear", "  ", "Orange");
  List<String> list = stream.filter(s -> s != null && !s.isBlank()).collect(Collectors.toList());
  System.out.println(list);

//map
//输出为map相对复杂， 但是似乎不怎么常见？？？？
```



***其他常用操作***

* 排序：`sorted()方法` , 如果要自定义排序规则， 可以传入一个`comparator对象`

* 去重  `distinct()` 方法

* 截取：  截取操作常用于把一个无限的`Stream`转换成有限的`Stream`，`skip(N)`用于跳过当前`Stream`的前N个元素，`limit(N)`用于截取当前`Stream`最多前N个元素

* 合并：将两个`Stream`合并为一个`Stream`可以使用`Stream`的静态方法`concat()`：

* flatMap: 

  ```
  Stream<List<Integer>> s = Stream.of(
          Arrays.asList(1, 2, 3),
          Arrays.asList(4, 5, 6),
          Arrays.asList(7, 8, 9));
  ```

  把上述`Stream`转换为`Stream`，就可以使用`flatMap()`：

  ```
  Stream<Integer> i = s.flatMap(list -> list.stream());
  ```

  因此，所谓`flatMap()`，是指把`Stream`的每个元素（这里是`List`）映射为`Stream`，然后合并成一个新的`Stream`：

* 并行操作

  通常情况下，对`Stream`的元素进行处理是单线程的，即一个一个元素进行处理。但是很多时候，我们希望可以并行处理`Stream`的元素，因为在元素数量非常大的情况，并行处理可以大大加快处理速度。

  把一个普通`Stream`转换为可以并行处理的`Stream`非常简单，只需要用`parallel()`进行转换：

  ```
  Stream<String> s = ...
  String[] result = s.parallel() // 变成一个可以并行处理的Stream
                     .sorted() // 可以进行并行排序
                     .toArray(String[]::new);
  ```

  经过`parallel()`转换后的`Stream`只要可能，就会对后续操作进行并行处理。我们不需要编写任何多线程代码就可以享受到并行处理带来的执行效率的提升。

* 其他操作......



## 4. Stream小结(*)

`Stream`提供的常用操作有：

转换操作：`map()`，`filter()`，`sorted()`，`distinct()`；

合并操作：`concat()`，`flatMap()`；

并行处理：`parallel()`；

聚合操作：`reduce()`，`collect()`，`count()`，`max()`，`min()`，`sum()`，`average()`；

其他操作：`allMatch()`, `anyMatch()`, `forEach()`。