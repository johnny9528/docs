# Java中的正则表达式

正则表达式不分语言， 但是不同语言的正则表达式引擎可能不同， 即思想一致， 写法可以不同。

## 1. 匹配规则

***精确匹配***

和字符串 `equals()`方法原理上没有区别。但是如果是特殊字符， 需要用 \ 进行转移

```java
//      如果需要转义， 则需要加上反斜杠， Java写法中， 在字符串中加入两个反斜杠

//example：
String re = "abc";
sout("abc".matches(re)) //true
  
String re2 = "a\\&c";
sout("a&c".matches(re2)) // true
```

精确匹配也可以匹配中文， 使用中文的Unicode编码。 但是没有必要， 原因还是因为精确匹配本身没有必要。



***匹配任意字符***

`.`  : 点符号可以匹配任意字符， 比如 `a.c` 可以匹配：

* `abc`
* `a&c`
* `acc`
* ......



***匹配数字***

`\d` : 代表一个数字， 同理， Java写法中， 需要加上两个反斜杠， 比如`abc\\d`

* `abc1`
* `abc3`
* `abc0`
* ......



***匹配字母， 数字或下划线***

`\w` : w的意思是word， 即在word中合理的字符可以匹配， 即字母， 数字或者下划线



***匹配空格字符***

`\s` : 匹配一个空格， 空格包括` ` , 也包括tab字符（Java中用`\t` 表示）



***匹配非数字***

`\D` : 非数字字符

同理， 

`\S` : 匹配非空格字符

`\W` : 匹配非word中的字符， 即非数字， 非字母， 非下划线



***重复匹配***

`*` : 可以匹配任意个字符， 0个或者多个

`+` : 匹配多个字符， 不包括0个， 大于0都行

`?` : 匹配0个或者1个

`{n}` : 精确指定n个字符

`{n, m} : 指定匹配 n-m个字符`

`{n, } : 匹配至少n个字符`



## 2. 复杂匹配规则

***匹配开头和结尾***

`^` 表示开头， `$`表示结尾



***匹配指定范围***

`[...]` : 方括号中指定范围， 比如：

* `[1457]abc` : 第一个字符只能是1， 4， 5， 7
* `[2-8]abc` :  2到8的数字
* `[0-9a-fA-F]` : 匹配大小写不限制的十六进制数

也可以使用不包括

`[^...]`

* `[^0-9]{3}` : 不包含数字的三个字符
* ......



***或匹配规则***

* `abc|def` : abc或者def



***使用括号*** 

将公共部分提取出来

* `person(1|2|3)` : person1 或者person2或者person3
* ......



## 3. 分组匹配

`java.util.regex` 包里面的`Pattern` 类和 `Matcher` 类

利用括号进行分组后， 可以匹配得到多个， 类似于一个数组， 然后可以获取每一个的值

（爬虫时使用python中的正则就用到了很多这个思路）

以代码为例：

```java
Pattern pattern = Pattern.compile("(\\d{3, 5})-(\\d{7, 8})");
//pattern对象可以用很多次
pattern.matcher("010-12345678").matches(); // true;
pattern.matcher("333-3333333").matches(); //true

获取Matcher对象
Matcher matcher = pattern.matcher("010-12345678");

if(matcher.matches() == true) {
  String first = matcher.group(1); //获取第一个 即“010”
  String second = matcher.group(2); // 获取第二个 即“12345678”
  String whole = matcher.group(0); //获取整个 即 “010-12345678”
}
```



## 4. 非贪婪匹配

正则表达式默认使用的是贪婪模式， 即前面的分组尽可能匹配更多的字符。但是有时候， 需要后面的分组匹配字符时， 有可能匹配不到， 因为被前面的分组占用， 此时在分组中加上  ？  则会变成非贪婪模式

比如该问题： 希望获得一串数字末尾0的个数， 比如23455000， 我们预期的结果是3。代码如下

```java
Pattern pattern = Pattern.compile("(\\d+)(0*)");
Matcher matcher = pattern.matcher("23455000");
if(matcher.matches()) {
  String s1 = matcher.group(1) // 23455000
  String s2 = matcher.group(2) // ""
}
```

上述结果并不是我们设想的那样， 原因就在于第一个分组贪婪的匹配了所有的数字， 因此。需要进行如下修改：

```java
Pattern pattern = Pattern.compile("(\\d+?)(0*)");
Matcher matcher = pattern.matcher("23455000");
if(matcher.matches()) {
  String s1 = matcher.group(1) // 23455
  String s2 = matcher.group(2) // 000
}
```



自我总结， 非贪婪即在能够正确匹配的前提下(可以存在很多中匹配结果)， 考虑带有非贪婪模式分组下的优先级降低。



## 5. 搜素和替换

使用正则表达式可以实现更灵活的分割字符串。

`String.split()` 方法中传入的就是正则表达式

* `"a,   b;;;  c  ".split("[\\,\\;\\s]+") ` :  {"a", "b", "c"}
* ......

***搜索***

```java
String s = "the quick brown fox jumps over the lazy dog.";
Pattern p = Pattern.compile("\\wo\\w");
Matcher m = p.matcher(s);
while (m.find()) {
		String sub = s.substring(m.start(), m.end());
    System.out.println(sub)
}
//最终结果输出 ： “row”, "dog" 
```

获取到`Matcher`对象后，不需要调用`matches()`方法（因为匹配整个串肯定返回false），而是反复调用`find()`方法，在整个串中搜索能匹配上`\\wo\\w`规则的子串，并打印出来。这种方式比`String.indexOf()`要灵活得多，因为我们搜索的规则是3个字符：中间必须是`o`，前后两个必须是字符`[A-Za-z0-9_]`。



***替换字符串***

```java
String s = "The     quick\t\t brown   fox  jumps   over the  lazy dog.";
String r = s.replaceAll("\\s+", " ");
System.out.println(r); // "The quick brown fox jumps over the lazy dog."
```



***反向引用***

如果我们要把搜索到的指定字符串按规则替换，比如前后各加一个`xxxx`，这个时候，使用`replaceAll()`的时候，我们传入的第二个参数可以使用`$1`、`$2`来反向引用匹配到的子串。例如：

```java
String s = "the quick brown fox jumps over the lazy dog.";
String r = s.replaceAll("\\s([a-z]{4})\\s", " <b>$1</b> ");
System.out.println(r);
//输出： the quick brown fox jumps <b>over</b> the <b>lazy</b> dog.
```

把任何4字符单词的前后用`xxxx`括起来。实现替换的关键就在于`" $1 "`，它用匹配的分组子串`([a-z]{4})`替换了`$1`。