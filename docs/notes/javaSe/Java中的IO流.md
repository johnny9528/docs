# Java中的IO流



## 1. File对象

构造 ***File*** 对象， 需要传入文件的路径

```java
File f = new File("D:\\forTest\\a.txt"); //Wondows下的绝对路径
File f = new File("a.txt"); //windows下相对路径
File f = new File(".\\forTest\\a.txt"); 
File f = new File("..\\forTest\\a.txt")
  //   	.表示同级目录， ..表示上一级目录
  // Linux中， 全部都是 / ， Windows都是 \\
```



### 1.1 根据文件对象获取路径

```java
String getPath() // 返回构造方法传入的路径字符串
String getAbsolutePath() //返回文件的绝对路径
String getCanonicalPath() //获取绝对路径， 但是会把 . 和.. 规范为真实的值
```

### 1.2 文件和目录

```File``` 对象既可以表示文件， 又可以表示目录

```java
boolean isFile() // 判断是否是一个文件
boolean isDirectory() // 判断是否是一个目录  

//如果是一个文件
boolean canRead() //是否可读
boolean canWrite()
boolean canExecute() //是否可执行
long length() //文件字节大小
boolean createNewFile() //创建新文件
boolean delete() //删除文件
  
createTempFile() //创建临时文件
deleteOnExit() //退出JVM时自动删除临时文件
 
//如果是一个目录
boolean canExecute() // 是否能列出它包含的文件的子目录
boolean mkdir() //创建当前File对象的目录
boolean mkdirs() //创建目录， 并在必要时同时创建不存在的父目录
boolean delete() // 删除当前目录， 当前目录为空时才能删除成功
  

//遍历文件和目录
list()
listFiles() //该方法含有很多重载方法， 可以过滤得到想要的文件
```



### 1.3 Path对象

该对象和```File``` 对象有类似的功能， 如果对目录进行复杂的遍历，拼接等操作， Path对象更方便。



## 2. InputStream(字节流)

```InputStrean```  不是接口， 是一个抽象类， 里面定义了一个最重要的抽象方法， ```read()```, 该方法具有一些重载形式， 该方法会读取输入流的下一个字节， 并返回字节表示的 ```int``` 值（0-255）。如果读到末尾， 返回 ```-1```  。

### 2.1 FileInputStrean

```FileInputStram```  是 ```InputStream``` 的子类， 可以从文件流中读取数据。 用法如下：

```java
InputStream input = null;
try {
  input = new FileInputStream("a.txt");
  int n;
  while((n = input.read()) != -1) {
    sout(n); //简写标准输出
  }
}finally {
  if(input != null) {
    input.close(); //  关闭流的操作
  }
}


// 另一种推荐写法， 自动关流（和python中类似）
 try(InputStream input = new FileInputStream("a.txt")) {
   int n;
   while((n = input.read()) != -1) {
     //.......
   }  // 此处编译器会自动写入finally并调用close()
 }

```



### 2.2 缓冲

一次读取一个字节很低效， 因此```InputStream``` 提供了两个 ```read()``` 的重载方法来支持读取多个字节

* ```int read(byte[] b)```  : 读取若干个字节填充到 ```byte[]```  数组中， 返回读取的字节数
* ```int read(byte[] b, int off, int len)``` : 指定数组的偏移量和最大填充数

```java
try(InputStream input = new FileInputStream('a.txt')) {
  byte[] buffer = new byte[1000]; //定义一个1000个字节大小的缓冲区
  int n;
  while((n = input.read(buffer)) != -1) {
    sout(n);//打印每次读取的字节数
  }
}
```



### 2.3 ByteArrayInputStream(了解)

在内存中直接模拟一个字节流的输入， 不用在定义的时候， 需要传入一个真实的文件地址， 传入的是一个byte数组



## 3. OutputStream

和 ```InputStream```  对应， 是一个标准输出流， 字节流， 抽象类， 定义一个抽象方法 ```void write(int b)```  。虽然传入的参数是一个 ```int```  参数， 但是只会写入一个字节， 即写入 ```int```  最低8位表示字节的部分（ b & 0xff）；

```flush()``` 方法， 将缓冲区的内容真正输入到目的地。

需要注意的是， 此处的缓冲区是底层的缓冲区， 包括 ```InputStream``` 也有缓冲区， 该缓冲区意思是， 操作系统不可能真的每写一个字节， 就立刻写入到文件中，而是本质上维护了一个byte数组， 数组满了就写入一次， 或者在 ```close()``` 方法之前会进行最后一次的 ```flush()``` ， 这些是默认调用该方法。 但是程序中可以自己调用该方法， 在缓冲区没有慢的时候， 写入到文件中。

在 ```InputStream``` 或者 ```OutputStream``` 中， 可以重载 ```read(byte[])```  或者 ```write(byte[])```  中， 也定义了一个缓冲区， 此处的缓冲区是我们自己本身定义的缓冲区， 与 ```flush()``` 底层的缓冲区有所区别。

### 3.1 FileOutputStream

与 ```FileInputStream``` 同理，使用如下：

```java
try(OutputStream output = new FileOutputStream("a.txt")) {
  output.write(72);  //写入单个字节  H
  output.write(101);  // e
  output.write("hello".getBytes("UTF-8"));
}
```



### 3.2 ByteArrayOutputStream(了解)

内存中模拟输出流



## 4. Filter模式

装饰者模式是一个很重要的设计模式。 在输入输出流中， 如果我们想添加其他的功能， 

以 ```InputStream```  为例：```InputStream```  分为两大类

一类是直接提供数据基础：

* FileInputStream
* ByteArrayInputStream
* servletInputStream
* ...

另一类是提供了附加功能的：

* BufferedInputStream (常用)
* DigistInputStream
* ...

利用装饰者模式， 可以给基础的输入流增加各种功能， 只需要一层一层的不断装饰即可。

```java
InputStream file = new FileInputStream("a.txt");
//创建一个基本的文件输入流

InputStream buffered = new BufferedInputStream(file);
// 包装一层， 提供缓冲功能

InputStream gzip = GZIPInputStream(buffered);
// 继续包装一层， 实现读取zip文件功能

//.......
```

也可以自己编写一个具备自定义功能的输入流， 然后包装上去。



## 5. 序列化

把java对象编程二进制内容（本质上是一个 ```byte[]```  数组）， 便于网络传输。

在对象中， 往往是 ```javaBean```  ， 实现序列化接口  ```java.io.Serializable```

```java
public interface Serializable {
  	
}
//该接口内没有定义任何方法， 是一个空接口， 即一个标记接口
```



实现序列化， 使用的是 ```ObjectInputStream``` 和 ```ObjectOutputStream``` 来进行读写， 可以对实现了序列化接口的任意对象进行读写。

***序列化id*** 

```serialVersionUID``` : 用于标识序列化的版本， 可以自动阻止不匹配的class版本。



## 6. Reader(字符流)

### 6.1 基本使用

区别在于， ```Reader``` 是一个字符流， 以 ```char``` 为单位进行读取， 读取范围为（0-65535）；

方法： ```int read(char[] c)```

使用上和字节流没有区别， 只是读取方式不同， 每次读取一个字符。但是需要注意的是， 字符设计到编码问题， 不同编码方式， 一个字符对应不同的字节数。 所以需要制定编码方式

```java
Reader reader = new FileReader("a.txt", StandardCharsets.UTF_8);
```



### 6.2. CharArrayReader  和 StringReader

和字节流中的 ```ByteArrayInputStream``` 类似， 前者是可以直接将字符数组作为数据源， 后者可以将字符串作为数据源。（简单了解）



### 6.3 Reader与InputStream关系 ——InputStreamReader

```Reader``` 实际上是基于 ```InputStream``` 构造的， 内部实际上通过一个字节流读取然后根据编码规则进行转码。因此， 可以通过字节流来构造字符流对象。

```java
InputStream input = new FileInputStream("a.txt");

Reader reader = new InputStreamReader(input, "UTF-8")；
  
//或者简写为如下
try(Reader reader = new InputStreamReader(new FileInputStream("a.txt"))) {
  //TODO
}
```



## 7. Writer

与 ```Reader``` 完全对应



## 8. PrintStream 和PrintWriter

这是两种标准输出流， 前者是字节， 后者是字符。 系统的输出语句 ```System.out.println()``` 实际上就是使用前者， 只不过是系统默认提供的。 

做了解即可。