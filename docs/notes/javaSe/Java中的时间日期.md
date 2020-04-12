# Java中的时间日期

## 1. Java8之前的时间日期类

Java8之前， 使用的是 ```java.util``` 下的 ```Data``` （基本）  ，```Calendar``` （在 ```Data``` 基础之上增加了时间的运算） , ```TimeZone``` (时区相关)。

旧的API存在很多历史遗留问题， 简单了解。

Java 8 之后， 新的时间日期api 定义在`java.time`这个包里面， 分别为 `LocalDateTime` , `ZonedDateTime` , `ZoneId` 

主`LocalDateTime`、`ZonedDateTime`、`ZoneId`等

## 2. LocalDateTime

```java
LocalDateTime dt = LocalDateTime.now() //静态方法， 获取当前的时期和时间

LocalTime t = LocalTime.now() // 获取当前的时间
  
LocalDate d = LocalDate.now() // 获取当前的日期
  
//  上述三种方式得到的时间/日期， 都是ISO 8601格式
```



由于执行代码会消耗时间， 上述三种方式得到时间不是严格对得上。 保证获得同一时刻的时间和日期， 采用如下方式：

```java
LocalDateTime dt = LocalDateTime.now();

LocalDate d = dt.toLocalDate();

LocalTime t = dt.toLocalTime();
```



上述的时间日期都是本地时间， 如果要指定时间和日期， 采用如下方式

```java
LocalDateTime dt = LocalDateTime.of(2020, 2, 29, 18, 18, 58);

LocalDate d = LocalDate.of(2020, 2, 29);

LocalTime t = LocalTime.of(11, 11, 11);

LocalDateTime dt2 = LocalDateTime.of(d, t); //通过时间对象和日期对象创建
```



通过字符串构造时间日期， 由于严格按照 `ISO 8601` 格式， 因此字符串也必须传入该种格式。

```java
LocalDateTime dt = LocalDateTime.parse("2020-02-29T11:11:11");
LocalDate d = LocalDate.parse("2020-02-29");
LocalTime t = LocalTime.parse("11:11:11");
```



`ISO 8601` 格式标准如下：

* 日期和时间分割符为 `T` 
* 日期： `yyyy-MM-dd` 
* 时间： `HH:mm:ss`
* 带毫秒： `HH:mm:ss.SSS`



如果要自定义输出格式， 可以将自定义格式字符串解析成标准的 `LocalDateTime` ，采用`DateTimeFormatter`

```java
DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy/MM/DD HH:mm:ss");
//自定义格式
sout(dtf.format(LocalDateTime.now())); //输出

LocalDateTime dt = LocalDateTime.parse("2019/11/30 12:12:12", dtf)
  //用自定义格式解析
```



对日期和时间进行操作， 加减操作， 该方法返回值仍然是一个`LocalDateTime` 对象， 因此多个方法之前可以链式调用。

```java
LocalDateTime dt = LocalDateTime.of(2019, 1, 23, 21, 11, 43);

LocalDateTime dt1 = dt.minusMonths(1).plusDays(5).minusHours(3);
//减去1个月， 加上5天， 减去3个小时
```



对时间的操作似乎并不常用， 日常中使用更多的是获取， 格式化时间日期， 因此其他方法做个简单了解，之后可以查询api获取更多应用

***其他方法简述***

* `isBefore()` : 判断时间或者日期的先后
* `isAfter()` :
* `with()` :该方法允许做一些复杂运算



***其他使用类***

使用 `Duration`  和 `Period` 中的某些方法， 可以表示两个时间或者日期的区间间隔。



## 3. ZonedDateTime和ZoneId

`ZonedId` 是 `java.time` 引入的时区类， `ZonedDateTime` 就是 `LocalDateTime` 和 `ZonedId` 的结合。

```java
ZonedDateTime zbj = ZonedDateTime.now();//默认时区下的时间

ZonedDateTime zny = ZonedDateTime.now(ZoneId.of("America/New_York"));
//指定时区获取当前时间

//
LocalDateTime dt = LocalDateTime.of(2019, 9, 11, 13, 13, 13);
ZonedDateTime zbj = dt.atZone(ZoneId.systemDefault());
//默认时区下的时间
ZonedDateTime zny = dt.atZone(ZoneId.of("America/New_York"));


//时区转换
withZoneSameInstant()   //方法
```

***转换时区操作， 尽量不要自己使用加减操作， 因为美国会有夏令时和冬令时的区别， 自己转换会出问题***



## 4. Instant

获取当前的时间戳， 可以用系统中的 `System,currentTimeMillis()` 方法， 也可以使用`Instant`对象。

```java
Instant now = Instant.now();

sout(now.getEpochSecond()); //获取 秒
sout(now,toEpochMilli()); // 毫秒
```



