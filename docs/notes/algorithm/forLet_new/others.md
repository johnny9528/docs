# 其他问题

```
229     315  331  334  373  801  890(模式匹配)  918(Kanade 算法)  981  992
```



## 7. 整数反转

### 题目描述

[链接](https://leetcode-cn.com/problems/reverse-integer/)

```
给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。
输入: 123
输出: 321
```

### 分析

直接用余数和除数就行，但是需要注意的是，在中间计算的时候，出现整形溢出，需要额外处理， 学一下这种处理思路

### 实现

```java
class Solution {
    public int reverse(int x) {
        int answer = 0;
		int temp = 0;
		while(x != 0) {
			temp = x % 10;
            // 处理整型溢出
			if(answer > Integer.MAX_VALUE / 10 || (answer == Integer.MAX_VALUE && temp > 7)) {
				return 0;
			}else if(answer < Integer.MIN_VALUE / 10 || (answer == Integer.MIN_VALUE && temp < -8)) {
				return 0;
			}
			answer = answer * 10 + temp;
			x = x / 10;
		}
		
		return answer;
	}
    
}
```





## 9 回文数(E）

### 描述

[链接](https://leetcode-cn.com/problems/palindrome-number/)

```
判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

输入: 121
输出: true

输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

### 分析

先判断基本情况，如果数字为负数，肯定不是回文数， 如果数字为0， 则是回文数。如果数字不是0，同时能被10整除， 一定不是回文数(数字不能最高位不能是0)。

思路1：转化为字符串，即判断是否是回文串

思路2： 反转数字， 比对二者是否相等(注意溢出问题)

思路3：根据回文数的特性， 反转数字不用进行到底， 只需要进行一半。比如123321， 进行一半之后，左边为123，右边为123，二者相等。此时需要注意位数为奇数的情况， 比如1234321， 进行一半之后， 左边为123， 右边为1234，此时只需判断 left = right/10。

### 实现

```java
//思路3：
class Solution {
    public boolean isPalindrome(int x) {
        if(x == 0) return true;
        if(x < 0 || x % 10 == 0) return false;
        int y = 0;
        while(x > y) {
            y = y * 10 + (x % 10);
            x = x / 10;
        }
        // System.out.println("" + x + "  " + y);
        return x == y || x == y / 10;       
    }
}

//思路1：
class Solution {
    public boolean isPalindrome(int x) {
        if(x == 0) return true;
        if(x < 0 || x % 10 == 0) return false;
        String s = "" + x;
        int left = 0, right = s.length() - 1;
        while(left <= right) {
            if(s.charAt(left) != s.charAt(right)) return false;
            left += 1;
            right -= 1;
        }
        return true; 
    }
}
```





## 29. 两个数相除

### 题目描述

[链接](https://leetcode-cn.com/problems/divide-two-integers/)

```
给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。
返回被除数 dividend 除以除数 divisor 得到的商。
整数除法的结果应当截去（truncate）其小数部分，例如：truncate(8.345) = 8 以及 truncate(-2.7335) = -2

输入: dividend = 10, divisor = 3
输出: 3
解释: 10/3 = truncate(3.33333..) = truncate(3) = 3


其他要求：
被除数和除数均为 32 位有符号整数。
除数不为 0。
假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−231,  231 − 1]。本题中，如果除法结果溢出，则返回 231 − 1。
```

### 分析

采用快速幂乘法类似的方法。比如20 / 3，分为以下几个步骤， 设置 临时 `b=3`， `temp=1`

*  `20` 大于`3 * 2`， 更新`b=6, temp=2`
* `20`大于`6 * 2`， 更新`b=12,temp=4`
* `20`小于`12 * 2`， 结束本轮， `ans = temp + div(20-12, 3)`
* 继续下一个，也就是 `8`除以`3`

细节上，需要考虑整型的溢出问题，由于负数的表达范围比整数大，因此，把整数转化为负数来计算

### 实现

```java
class Solution {
    public int divide(int dividend, int divisor) {
        // 除法溢出只有一种情况，那就是  MIN_VALUE  除以 -1
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE;
        }

        boolean flag = (dividend > 0) ^ (divisor > 0); // 全是正数或者全是负数， flag=false,反之
		
        // 将正数转化为负数进行计算
        if (dividend > 0) {
            dividend = 0 - dividend;
        }
        if (divisor > 0) {
            divisor = 0 - divisor;
        }

        int ans = div(dividend, divisor);

        return flag == true ? -ans : ans;

    }

    /**
        a 和 b 都是负数， 因为负数的表示范围更大，全部转化为负数来计算
    */
    private int div(int a, int b) {
        if (a > b) {
            // 因为都是负数，a > b， 说明 a的绝对值比b小，相除等于0，不用递归了
            return 0;
        }
        int num = b;
        int count = 1;
        
        // while循环里面的 (num + num < 0)的作用是防止计算的时候，num已经小于Integer.MIN_VALUE 的一半的时候，相加会溢出，相加会变成正数，此时不应该继续while了
        while ((a <= num + num) && (num + num < 0)) {
            // num 还能继续乘以2扩充
            num = num + num;
            count = count + count;
        }
		// 继续递归计算剩下的
        return count + div(a - num, b);
    }
}
```





## 50. 快速幂计算乘方(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/powx-n/)

```
实现 pow(x, n) ，即计算 x 的 n 次幂函数。
```

### 实现

快速幂，和除法有类似的地方。可以用递归，可以用迭代

### 实现

```java
class Solution {
    public double myPow(double x, int n) {
 
        long count = n;
        if (n < 0) { // 转化为正数计算
            count = -(long) n;
        }
        double ans = 1.0;
        double value = x;

        while (count > 0) {
            if (count % 2 == 1) {
                ans *= value;
            }
            value = value * value;

            count = count / 2;
        }


        if (n >= 0) {
            return ans;
        } else {
            return 1 / ans;
        }
    }
}
```





## 71. 简化路径(M)

### 描述

[链接](https://leetcode-cn.com/problems/simplify-path/)

```
以 Unix 风格给出一个文件的绝对路径，你需要简化它。或者换句话说，将其转换为规范路径。
在 Unix 风格的文件系统中，一个点（.）表示当前目录本身；此外，两个点 （..） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂相对路径的组成部分。更多信息请参阅：Linux / Unix中的绝对路径 vs 相对路径
请注意，返回的规范路径必须始终以斜杠 / 开头，并且两个目录名之间必须只有一个斜杠 /。最后一个目录名（如果存在）不能以 / 结尾。此外，规范路径必须是表示绝对路径的最短字符串

输入："/home/"
输出："/home"
解释：注意，最后一个目录名后面没有斜杠。

输入："/a/./b/../../c/"
输出："/c"

输入："/home//foo/"
输出："/home/foo"
解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。

```

### 分析

栈的使用，先用split函数对字符串进行分割，如果遇到".."，则出栈， 如果是"."， 什么都不变， 如果是正常字符，则压栈。

### 实现

```java
class Solution {
    public String simplifyPath(String path) {
        Deque<String> stack = new LinkedList<>();
        for (String item : path.split("/")) {
            if (item.equals("..")) {
                if (!stack.isEmpty()) stack.pop();
            } else if (!item.isEmpty() && !item.equals(".")) stack.push(item);
        }
        String res = "";
        for (String d : stack) res = "/" + d + res;
        return res.isEmpty() ? "/" : res;  
    }
}
```



## 146. LRU缓存机制(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/lru-cache/)

```
运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。
 

进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？

输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

### 分析

自己实现，需要一个`map`和自定义的双向链表。

`put`操作：

* 如果`key`不存在`map`中，那么需要构造一个节点，然后插到双向链表头部，同时，如果超了个数的话，需要删除链表的尾巴
* 如果key存在，需要定位到节点，并把节点移动到头部

`get`操作

* 直接`get`即可

### 实现

```java
class LRUCache {

    // 自定义的双向链表
    class LinkedNode {
        int key;
        int value;
        LinkedNode prev;
        LinkedNode next;

        LinkedNode(int key, int value) {
            this.key = key;
            this.value = value;
        }

        LinkedNode() {

        }

    }


    private Map<Integer, LinkedNode> map;  // map保存
    int capacity; // 容量
    int size; // 当前的个数
    LinkedNode preHead; // 头节点的上一个节点
    LinkedNode tail; // 尾巴的下一个节点

    public LRUCache(int capacity) {
        map = new HashMap<>();
        this.capacity = capacity;
        size = 0;

        preHead = new LinkedNode();
        tail = new LinkedNode();
        preHead.next = tail;
        tail.prev = preHead;
    }
    
    public int get(int key) {
        if (map.containsKey(key)) {
            moveNodeToHead(map.get(key));
            return map.get(key).value;
        } else {
            return -1;
        }
    }
    
    public void put(int key, int value) {

        // 如果key不存在，则插入
        if (map.containsKey(key) == false) {
            LinkedNode newNode = new LinkedNode(key, value);
            // 插入到头部
            insertToHead(newNode);
            size += 1;
            map.put(key, newNode);

            // 如果个数超了，需要删除尾巴
            if (size == capacity + 1) {
                LinkedNode lastOne = deleteTail(); // 链表中删除尾巴
                map.remove(lastOne.key);  // map中也要删除
            }
        } else {
            // 已经存在了
            LinkedNode node = map.get(key);

            // 更改其值
            node.value = value;
            // 移动到头部
            moveNodeToHead(node);
        }
    }
	
    // 插入新的节点到头部
    private void insertToHead(LinkedNode node) {
        node.next = preHead.next;  
        preHead.next.prev = node;
        preHead.next = node;
        node.prev = preHead;
    }
	
    // 删除尾巴节点
    private LinkedNode deleteTail() {
        LinkedNode temp = tail.prev.prev;
        LinkedNode ans = temp.next;
        temp.next = tail;
        tail.prev = temp;
        size -= 1;
        return ans;
    }

	// 将已有的节点移动到头部
    private void moveNodeToHead(LinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;

        insertToHead(node);
    }
}

```



## 460. LFU缓存(H)

### 题目描述

[链接](https://leetcode-cn.com/problems/lfu-cache/)

```
请你为 最不经常使用（LFU）缓存算法设计并实现数据结构。
实现 LFUCache 类：

LFUCache(int capacity) - 用数据结构的容量 capacity 初始化对象
int get(int key) - 如果键存在于缓存中，则获取键的值，否则返回 -1。
void put(int key, int value) - 如果键已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量时，则应该在插入新项之前，使最不经常使用的项无效。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除 最久未使用 的键。
注意「项的使用次数」就是自插入该项以来对其调用 get 和 put 函数的次数之和。使用次数会在对应项被移除后置为 0 。

进阶：

你是否可以在 O(1) 时间复杂度内执行两项操作？
```

### 分析

`LFU`意识是最不经常使用，即某个元素，如果使用频率最低，那么如果空间不够了，需要将其删除。因此需要有一个频率的变量。因为都要是常数时间复杂度，只能用 `map`。 很明显，`key`和`value`必须有一个`map`， 因为要常数时间实现删除，因此，必须能直接得到某个元素的频率并且删除，于是用另一个`map`， `key`是频率， 比如 `1, 2, 3, 4...`， 由于受到容量限制，这个频率的个数并不会很大很大，`value`是频率对应的节点们，用一个双向链表进行保存。因为在频率相等的时候，删除哪一个，肯定是删除最早使用那个，因此需要删除头部的元素，需要双向链表保存。

得好好看看这两个缓存算法

### 实现

```java
class LFUCache {

    class Node {
        int key;
        int value;
        int freq;

        Node(int key, int value, int freq) {
            this.key = key;
            this.value = value;
            this.freq = freq;
        }
    }

    private int capacity;
    private Map<Integer, Node> nodeMap;  // 保存值的map
    private Map<Integer, LinkedList<Node>> freqMap;  // key 是频率，value是对应频率节点的
    private int minFreq; // 当前最小的使用频率 

    public LFUCache(int capacity) {
        this.capacity = capacity;
        nodeMap = new HashMap<>(capacity);
        freqMap = new HashMap<>();
        minFreq = 0;
    }
    
    public int get(int key) {
        if (capacity == 0) {
            return -1;
        }


        // get 操作，需要获取值，并且将对应的node的频率 + 1
        if (nodeMap.containsKey(key) == false) {
            // 并不存在对应的 key
            return -1;
        }

        Node node = nodeMap.get(key);  // 得到对应的节点
        int freq = node.freq;
        LinkedList<Node> list = freqMap.get(freq);  // 得到对应的频率链表，一定是存在的 

        // 在链表中删除该节点
        list.remove(node);
        // 删除之后，如果这个链表没有元素之后，说明这个频率没有节点了
        if (list.size() == 0) {
            freqMap.remove(freq);
            list = null;
            if (minFreq == freq) {
                minFreq += 1;
            }
        }

        // freq应该加1,并加入到 freq + 1的链表中
        node.freq += 1;
        freq += 1;
        if (freqMap.containsKey(freq) == false) {
            freqMap.put(freq, new LinkedList<Node>());
        }
        freqMap.get(freq).offerLast(node);

        return node.value;
    }
    
    public void put(int key, int value) {
        if (capacity == 0) {
            return;
        }


        // 需要检验是否在里面
        if (nodeMap.containsKey(key) == false) {
            // 需要检验是否已经满了
            if (nodeMap.size() == capacity) {
                // 说明刚好满了， 需要删除一个 minFreq的节点
                LinkedList<Node> list = freqMap.get(minFreq);
                Node node = list.pollFirst(); // 删除第一个节点
                nodeMap.remove(node.key);

                // 如果删了之后，这个频率没有节点了，需要删除 freqMap中的元素
                if (list.size() == 0) {
                    freqMap.remove(node);
                    list = null;
                }
            }

            // 加入元素的频率为1
            if (freqMap.containsKey(1) == false) {
                freqMap.put(1, new LinkedList<Node>());
            }
            Node newNode = new Node(key, value, 1);
            freqMap.get(1).offerLast(newNode);
            nodeMap.put(key, newNode);
            minFreq = 1;
        } else {
            // 在里面，之间更新即可
            get(key);  // 这一步是为了给使用频率加1， 因为效果其实是一样的
            nodeMap.get(key).value = value; // 更新节点值
        }

        
    }
}
```





## 155. 最小栈(E)

### 题目描述

[链接](https://leetcode-cn.com/problems/min-stack/)

```
设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

push(x) —— 将元素 x 推入栈中。
pop() —— 删除栈顶的元素。
top() —— 获取栈顶元素。
getMin() —— 检索栈中的最小元素。
```

### 分析

要常数时间内检索到最小元素，那么就一定需要存储当前时刻的最小元素，同时，存储值本身需要一个栈，因此使用两个栈，一个栈存储值，一个存储当前时刻的最小值。

入栈的时候，第一步将值入保存值的栈，然后存储最小值的栈的栈顶是之前的最小元素，那么与这一次的值相比，谁小，则将这个值入最小值栈。

出栈的时候，只需要将两个栈顶的元素弹出即可。

### 实现

```java
class MinStack {

    Deque<Integer> value_stack;
    Deque<Integer> min_stack;

    /** initialize your data structure here. */
    public MinStack() {
        value_stack = new LinkedList<>();
        min_stack = new LinkedList<>();
        min_stack.offerLast(Integer.MAX_VALUE); // 初始化，将最大值加进去，避免需要判空
    }
    
    public void push(int x) {

        value_stack.offerLast(x); // 先加入到 value_stack
        int currentMin = min_stack.getLast();

        min_stack.offerLast(Math.min(currentMin, x)); // 将此刻的最小值加入到 min_stack

    }
    
    public void pop() {
        // 将两个栈顶弹出即可
        value_stack.pollLast();
        min_stack.pollLast();
    }
    
    public int top() {
        return value_stack.getLast();
    }
    
    public int getMin() {
        return min_stack.getLast();
    }
}

```





## 201. 数字范围按位与(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)

```
给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。
输入: [5,7]
输出: 4
```

### 分析

数据范围太大，不能直接枚举所有的情况。

最终的结果其实就是 m 和 n 这两个二进制的公共前缀。后面的位都是0。因为如果m和n的某一个位不相同的话，因为是整个范围的按位与，那么这个范围内，一定在这一位上至少有一个0，一旦有一个0，那么与操作的结果就是0。因此，需要寻找最长的公共前缀。使用位移操作即可。

### 实现

```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        int count = 0;

        while (m != n) {
            m = m >> 1;
            n = n >> 1;
            count += 1;
        }

        return m << count;
    }
}
```





## 202. 快乐数(E)

### 题目描述

[链接](https://leetcode-cn.com/problems/happy-number/)

```
编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。如果 可以变为  1，那么这个数就是快乐数。
如果 n 是快乐数就返回 True ；不是，则返回 False 。

输入：19
输出：true
解释：
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

### 分析

一直计算，如果遇到1，则是快乐数，如果不是快乐数，那么一定会遇到环，即之前计算过的数字。因为如果数字超过三位数，那么经过计算，最终一定会降到三位数及其以下，充其量，三位数枚举完，因此如果不是快乐数，一定会遇到环。

基于上述前提，可以一直计算，模拟这个过程，并用`set`记录已经计算过的值，遇到环或者遇到1则返回对应结果。

方法2，是不用`set`，利用判断链表中是否有环的思路，使用快慢指针进行判断

### 实现

```java
class Solution {
    public boolean isHappy(int n) {
        int slow = n;
        int fast = getNext(n);

        // 检测链表是否有环的思路，来检测快乐数是否有环
        while (fast != slow && fast != 1 && slow != 1) {
            slow = getNext(slow);
            fast = getNext(getNext(fast));
        }

        return fast == 1 || slow == 1;
    }


    // 计算下一个值
    private int getNext(int n) {
        int ans = 0;

        while (n > 0) {
            int extra = n % 10;
            ans += extra * extra;
            n = n / 10;
        }

        return ans;
    }
}
```





## 204. 小于n的质数的个数(E)

### 题目描述

[链接](https://leetcode-cn.com/problems/count-primes/)

```
统计所有小于非负整数 n 的质数的数量
输入：n = 10
输出：4
解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
```

### 分析

枚举所有值并判断是否是质数，复杂度太高。

使用一个额外的数组，如果某个数是质数，那么其 2 倍，3倍数...... 则都不是质数。那么从2开始，2是质数，然后去计算2的2倍，3倍......，这些值都不是质数，然后继续从3开始，算3的倍数。

可以优化，计算数字 `i`，的时候，在 `i * i`之前的倍数，都已经被计算过了。

### 实现

```java
class Solution {
    public int countPrimes(int n) {

        boolean[] isPrime = new boolean[n + 1];
        Arrays.fill(isPrime, true);  // 先将所有值都设置位 true, 即默认都是质数
        int ans = 0;

        for (int i = 2; i < n; i++) {
            if (isPrime[i] == false) {
                // 不是质数，不用继续了
                continue;
            }

            ans += 1;  // 这个数是质数

            if ((long) i * i < n) {
                for (int j = i * i; j <= n; j += i) {  // 将他的倍数都设置位 不是质数
                    isPrime[j] = false;  
                }
            }
        }

        return ans;
    }
}
```

 



## 225. 用队列实现栈(E)

### 题目描述

[链接](https://leetcode-cn.com/problems/implement-stack-using-queues/)

```
使用队列实现栈的下列操作：

push(x) -- 元素 x 入栈
pop() -- 移除栈顶元素
top() -- 获取栈顶元素
empty() -- 返回栈是否为空

```

### 分析

假设之前有 `1 2 3 4 5`已经入栈了. 现在要将 6入栈，但是直接加入到队列尾部是  `6 1 2 3 4 5`, 因此需要将之前的全部拿出来从新放进去。

* 先将 6放到尾巴， 即 `6 1 2 3 4 5`
* 然后依次取出 `5 4 3 2 1`加到尾巴， 即 `1 2 3 4 5 6`
* 结束

### 实现

```java
class MyStack {

    Queue<Integer> queue;

    /** Initialize your data structure here. */
    public MyStack() {
        queue = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {

        int count = queue.size();  // 之前的个数
        queue.offer(x);

        for (int i = 1; i <= count; i++) {
            queue.offer(queue.poll());  // 把之前的全部拿出来，再放到尾部
        }


    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        return queue.poll();
    }
    
    /** Get the top element. */
    public int top() {
        return queue.peek();
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return queue.isEmpty();
    }
}

```





## 231. 2的幂(E)

### 题目描述

[链接](https://leetcode-cn.com/problems/power-of-two/)

```
给定一个整数，编写一个函数来判断它是否是 2 的幂次方。
```

### 分析

2的幂指数，二进制中只有一个1，因此使用位移操作判断即可

### 实现

```java
class Solution {
    public boolean isPowerOfTwo(int n) {

        // 只有一位是1，其他都是0
        int countOne = 0;
        while (n != 0) {
            if ((n & 1) == 1) {
                // 这一位是1
                countOne += 1;
            }

            if (countOne > 1) {
                return false;
            }

            n = n >> 1;
        }

        return countOne == 1;
    }
}
```





## 347. 前K个高频元素(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/top-k-frequent-elements/)

```
给定一个非空的整数数组，返回其中出现频率前 k 高的元素。
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

### 分析

和计算前K大的没啥大区别。当统计词频之后就转化为了这个问题。采用快速排序。

### 实现

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        // 统计词频
        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        List<int[]> list = new ArrayList<>(map.size());
        for (int key : map.keySet()) {
            list.add(new int[] {key, map.get(key)});
        }

        // 快速排序 寻找 topK
        quickSortForTopK(list, 0, list.size() - 1, k);

        int[] ans = new int[k];
        for (int i =1; i <= k; i++) {
            ans[i - 1] = list.get(list.size() - i)[0];
        }
        return  ans;
    }

    private void quickSortForTopK(List<int[]> list, int startIndex, int endIndex, int k) {
        if (startIndex >= endIndex) {
            return;
        }

        int key = list.get(startIndex)[1];

        int left = startIndex;
        int right = endIndex;

        while (left < right) {

            while (left < right && list.get(right)[1] >= key) {
                right -= 1;
            }
            Collections.swap(list, left, right);

            while (left < right && list.get(left)[1] <= key) {
                left += 1;
            }
            Collections.swap(list, left, right);
        }

               // 一趟快排结束
        if (list.size() - left == k) {
            return;
        } else if (k < list.size() - left) {
            quickSortForTopK(list, left + 1, endIndex, k);
        } else {
            quickSortForTopK(list, startIndex, left - 1, k);
        }
    }
}
```





## 386. 字典序排数字(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/lexicographical-numbers/)

```
给定一个整数 n, 返回从 1 到 n 的字典顺序。
例如，
给定 n =1 3，返回 [1,10,11,12,13,2,3,4,5,6,7,8,9] 。

请尽可能的优化算法的时间复杂度和空间复杂度。 输入的数据 n 小于等于 5,000,000。

```

### 分析

数据太大，肯定不能使用排序。

这道题实际上是类似于一个 10叉树的前序遍历。

```2
            1    2   ...   9
           /|\ \
          0 1 2..9
```

先遍历1， 然后继续1的子树，即再遍历 10，再遍历11， 然后12...19。 然后开始2这个树的遍历

### 实现

```java
class Solution {
    public List<Integer> lexicalOrder(int n) {
        List<Integer> ans = new ArrayList<>(n + 1);

        if (n <= 9) {
            for (int i = 1; i <= n; i++) {
                ans.add(i);
            }
            return ans;
        }

        // n 是大于9的
        for (int i = 1; i <= 9; i++) {
 
            // 类似于10叉树的先序遍历
            dfs(ans, i, n);
        }

        return ans;
    }

    private void dfs(List<Integer> ans, int base, int n) {

        ans.add(base);  // 先将节点加进去，然后依次遍历子树,即先序遍历

        base = base * 10;  // 扩大10倍
        // 从 0 开始
        for (int i = 0; i <= 9; i++) {
            int newBase = base + i;
            if (newBase > n) {
                // 已经超过 n 了 
                return;
            }

            dfs(ans, newBase, n);  // 先序遍历
        }
    }


}
```





## 398. 随机数索引(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/random-pick-index/)

```
给定一个可能含有重复元素的整数数组，要求随机输出给定的数字的索引。 您可以假设给定的数字一定存在于数组中。

注意：
数组大小可能非常大。 使用太多额外空间的解决方案将不会通过测试。

int[] nums = new int[] {1,2,3,3,3};
Solution solution = new Solution(nums);

// pick(3) 应该返回索引 2,3 或者 4。每个索引的返回概率应该相等。
solution.pick(3);

// pick(1) 应该返回 0。因为只有nums[0]等于1。
solution.pick(1);

```

### 分析

不能使用太多空间，那肯定不能进行统计。

一种思路是，在找索引的时候，来统计需要找的值，然后随机取一个。

另一种思路是，遍历寻找，等于该值的时候，随机，看是否最终选他。并且是等概率的。看代码吧，这种思路，有点特殊，需要注意

### 实现

```java
class Solution {
    private int[] nums;
    public Solution(int[] nums) {
        this.nums = nums;
    }
    
    public int pick(int target) {
        int index = 0;
        int count = 0;
        for(int i = 0; i < nums.length; i++) {
            if(nums[i] == target) {
                count += 1;
                double ran = Math.random();
                double temp = (double)1 / count;
                if(ran <= temp) {
                    index = i;
                }
            }
        }
        return index;
    }
}

```





## 470. 用 `rand7`实现 rand10(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/implement-rand10-using-rand7/)

```
已有方法 rand7 可生成 1 到 7 范围内的均匀随机整数，试写一个方法 rand10 生成 1 到 10 范围内的均匀随机整数。

不要使用系统的 Math.random() 方法。

```

### 分析

能够随机生成1-7的整数，那么就可以得到随机的1-49的整数。取前40个，然后取余就得到0-9的随机的整数。如果大于40，拒绝，再来一次。

取40， 30， 20， 10 都可以，但是40的拒绝的概率最小的。

### 实现

```java
class Solution extends SolBase {
    public int rand10() {
        while(true) {
            int x = rand7();
            int y = rand7();

            if(x + (y - 1) * 7 <= 40) {
                return (x + (y - 1) * 7) % 10 + 1;
            }
        }
        
    }
}
```





## 622. 设计循环队列(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/design-circular-queue/)

```
设计你的循环队列实现。 循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”。

循环队列的一个好处是我们可以利用这个队列之前用过的空间。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。

你的实现应该支持如下操作：

MyCircularQueue(k): 构造器，设置队列长度为 k 。
Front: 从队首获取元素。如果队列为空，返回 -1 。
Rear: 获取队尾元素。如果队列为空，返回 -1 。
enQueue(value): 向循环队列插入一个元素。如果成功插入则返回真。
deQueue(): 从循环队列中删除一个元素。如果成功删除则返回真。
isEmpty(): 检查循环队列是否为空。
isFull(): 检查循环队列是否已满。

```

### 分析

循环队列，主要是要知道头部在哪，尾巴在哪，以及什么时候是空的，什么时候是满的。

采用数组保存数据，并保存头部和尾巴的索引下标。由于是循环队列，因此，需要在超出边界的时候取余。需要知道什么时候是空什么时候是满的，需要知道当前数据量的大小。

### 实现

```java
class MyCircularQueue {
    private int[] nums;
    private int headIndex; // 头部， 如果没有数据的时候，是个虚拟的头部
    private int tailIndex; // 尾部
    int size; // 数据的个数
    public MyCircularQueue(int k) {
        nums = new int[k];
        headIndex = 0;
        tailIndex = 0;
        size = 0;
    }
    
    public boolean enQueue(int value) {
        if (size == nums.length) {
            // 已经满了
            return false;
        } 
        
        if (size == 0) {
            // size = 0， 实际上 头部和尾部是不存在的，即没有意义的
            nums[tailIndex] = value;
            headIndex = tailIndex;
            size = 1;
        } else {
            tailIndex += 1; // 尾巴后移一个，并进行赋值
            if (tailIndex == nums.length) {
                tailIndex = 0;
            }
            nums[tailIndex] = value;
            size += 1;
        }
        return true;
    }
    
    public boolean deQueue() {
        if (size == 0) {
            return false;
        } 

        headIndex += 1; // 出队列是从头部出队列，因此头部后移一个即可
        if (headIndex == nums.length) {
            headIndex = 0;
        }
        size -= 1;
        return true;
    }
    
    public int Front() {
        if (size == 0) {
            return -1;
        }

        return nums[headIndex];
    }
    
    public int Rear() {
        if (size == 0) {
            return -1;
        }
        return nums[tailIndex];
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public boolean isFull() {
        return size == nums.length;
    }
}

```



## 641. 设计循环双端队列(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/design-circular-deque/)

```
设计实现双端队列。
你的实现需要支持以下操作：

MyCircularDeque(k)：构造函数,双端队列的大小为k。
insertFront()：将一个元素添加到双端队列头部。 如果操作成功返回 true。
insertLast()：将一个元素添加到双端队列尾部。如果操作成功返回 true。
deleteFront()：从双端队列头部删除一个元素。 如果操作成功返回 true。
deleteLast()：从双端队列尾部删除一个元素。如果操作成功返回 true。
getFront()：从双端队列头部获得一个元素。如果双端队列为空，返回 -1。
getRear()：获得双端队列的最后一个元素。 如果双端队列为空，返回 -1。
isEmpty()：检查双端队列是否为空。
isFull()：检查双端队列是否满了。

```

### 分析

和上面一样，无非就是多了个头部插入的操作。

### 实现

```java
class MyCircularDeque {
    private int[] nums;
    private int headIndex;
    private int tailIndex;
    private int size;
    
    public MyCircularDeque(int k) {
        nums = new int[k];
        headIndex = 0;
        tailIndex = 0;
        size = 0;
    }
    
    public boolean insertFront(int value) {
        if (size == nums.length) {
            // 已经满了
            return false;
        }
        if (size == 0) {
            // 如果之前为空，那么现在的 head 和 tail实际上是没有意义的
            nums[headIndex] = value;
            tailIndex = headIndex;
            size += 1;
        } else {
            headIndex -= 1; // head 前移一个
            if (headIndex == -1) {
                headIndex = nums.length - 1;
            }
            nums[headIndex] = value;
            size += 1;
        }
        return true;
       
    }
    
    public boolean insertLast(int value) {
        if (size == nums.length) {
            // 已经满了
            return false;
        }
        
        if (size == 0) {
            // 现在的 head 和 tail 是没有意义的
            nums[tailIndex] = value;
            headIndex = tailIndex;
            size += 1;
        } else {
            tailIndex += 1;
            if (tailIndex == nums.length) {
                tailIndex = 0;
            }
            nums[tailIndex] = value;
            size += 1;
        }
        return true;
    }
    
    public boolean deleteFront() {
        if (size == 0) {
            return false;
        }

        headIndex += 1;
        size -= 1;
        if (headIndex == nums.length) {
            headIndex = 0;
        }
        return true;
    }
    
    public boolean deleteLast() {
        if (size == 0) {
            return false;
        }

        tailIndex -= 1;
        size -= 1;
        if (tailIndex == -1) {
            tailIndex = nums.length - 1;
        }
        return true;
    }
    
    public int getFront() {
        if (size == 0) {
            return -1;
        }

        return nums[headIndex];
    }
    
    public int getRear() {
        if (size == 0) {
            return -1;
        }

        return nums[tailIndex];

    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public boolean isFull() {
        return size == nums.length;
    }
}

```





## 670. 交换两个位使得数字最大(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/maximum-swap/)

```
给定一个非负整数，你至多可以交换一次数字中的任意两位。返回你能得到的最大值。

输入: 2736
输出: 7236
解释: 交换数字2和数字7。

输入: 9973
输出: 9973
解释: 不需要交换。
```

### 分析

要最大，那么肯定要尝试交换最左边的数字，替换为右边最大的数字。并且注意这种情况， `2991`， 肯定是替换越后面的9才更大，即 `9921 > 9291`.

因此就是先尝试找比第一个数字大的，找不到，去尝试第二位数字，依次类推。为了方便，用数组去保存数字。可以用字符数组，也可以用数字数组。

### 实现

```java
class Solution {
    public int maximumSwap(int n) {
        if (n == 0) {
            return 0;
        }
		
        // 转化位数组保存
        int len = ("" + n).length();
        int[] nums = new int[len];
        for (int i = len - 1; i >= 0; i--) {
            nums[i] = n % 10;
            n = n / 10;
        }

        for (int i = 0; i < len; i++) {
            int maxIndex = i; // 找他后面的最大的数字 (相同的话，取越往后的，因此从后往前找)
            for (int j = len - 1; j >= i + 1; j--) {
                if (nums[j] > nums[maxIndex]) {
                    maxIndex = j;
                }
            }

            if (maxIndex != i) {
                // 存在比 i 更大的
                int temp = nums[i];
                nums[i] = nums[maxIndex];
                nums[maxIndex] = temp;
                break; // 只能换一次，找到了合适的就跳出整个循环
            }
        }

        int ans = 0;
        for (int i = 0; i < len; i++) {
            ans = ans * 10 + nums[i];
        }
        return ans;
    }

}
```





## 678. 有效的括号字符串(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/valid-parenthesis-string/)

```
给定一个只包含三种字符的字符串：（ ，） 和 *，写一个函数来检验这个字符串是否为有效字符串。有效字符串具有如下规则：

任何左括号 ( 必须有相应的右括号 )。
任何右括号 ) 必须有相应的左括号 ( 。
左括号 ( 必须在对应的右括号之前 )。
* 可以被视为单个右括号 ) ，或单个左括号 ( ，或一个空字符串。
一个空字符串也被视为有效字符串。

输入: "(*))"
输出: True
```

### 分析

括号匹配，一般都需要栈来辅助。由于有 星号的存在，因此不能只存储左括号的个数。遇到右括号，判断左括号是否存在。如果只保存个数的话，无法确定星号的位置，因为 `(*()`这种字符串，看上去左括号有两个，星号代表右括号的话也是两个，但是时机上是不正确的。因此必须保存左括号和星号的位置信息。

使用两个栈，一个表示左括号的索引，一个表示星号的索引。遍历字符串。

* 如果是左括号，加入到栈
* 如果是星号，加入到栈
* 如果是右括号，那么肯定优先匹配左括号,因为星号是万精油，如果左括号的栈不为空，则减少一个，否则只能尝试减少星号，让星号去充当左括号。

流程结束之后，两个栈可能都有剩余。此时星号要不代表右括号，要不代表空值。那么此时并不是星号数量大于左括号数量就合法了，因为还需要位置信息。每一对，左括号的位置必须小于星号的索引。

### 实现

```java
class Solution {
    public boolean checkValidString(String s) {
        if (s == null || s.length() == 0) {
            return true;
        }
        Deque<Integer> leftStack = new LinkedList<>(); // 存放左括号的索引
        Deque<Integer> starStack = new LinkedList<>(); // 存放 星号的索引

        for (int i = 0; i < s.length(); i++) {
            
            char c = s.charAt(i);

            if (c == '(') {
                // 等于左括号
                leftStack.offerLast(i);
            } else if (c == '*') {
                starStack.offerLast(i);
            } else {
                // 是右括号，需要消耗了
                if (leftStack.size() == 0 && starStack.size() == 0) {
                    return false;
                }

                if (leftStack.size() > 0) {
                    // 有真正的左括号，优先使用
                    leftStack.pollLast();
                } else {
                    starStack.pollLast();
                }
            }
        }


        // 最后看两个栈里面剩余的能不能匹配，现在 starStack 中的只能代表右括号或者星号
        if (leftStack.size() > starStack.size()) {
            return false;
        }

        // 现在个数满足，但是不确定里面是否满足
        while (leftStack.size() > 0 && starStack.size() > 0) {
            int index1 = leftStack.pollLast();
            int index2 = starStack.pollLast();
            if (index1 > index2) {
                // 如果 index1 的右侧没有 星号来充当 右括号了
                return false;
            }
        }

        return leftStack.size() == 0;
        
    }
}
```





## 856. 括号的分数(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/score-of-parentheses/)

```
给定一个平衡括号字符串 S，按下述规则计算该字符串的分数：

() 得 1 分。
AB 得 A + B 分，其中 A 和 B 是平衡括号字符串。
(A) 得 2 * A 分，其中 A 是平衡括号字符串。

输入： "(()(()))"
输出： 6
(1 + 1 * 2) * 2 = 6
```

### 分析

用栈进行模拟。栈里面需要保存临时计算的结果，是 `int `， 因此，如果是左括号，可以用`-1`进行标识

* 如果是左括号，左括号进栈, 即 `-1`进栈
* 如果是右括号， 此时
  * 如果栈顶是左括号，那么刚好二者匹配，左括号出栈，计算得1，如果此时栈顶是一个数字，那么需要和他合并，加起来，然后把这个结果入栈。 比如 栈里面是 `-1 3 -1`， 此时遇到右括号， 右括号和 `-1`匹配，贡献1， 和 `3`合并，最终得到 `-1 4`
  * 如果栈顶是数字， 比如 `-1 3 -1 4`， 那么需要这个数字需要乘以2， 再和之前的数字合并，最终得 `-1 11`, 其中 `11 = 4 * 2 + 3`
* 由于 字符串一定是合法的，那么最终栈里面只会剩下一个值，就是最终的结果。

### 实现

```java
class Solution {
    public int scoreOfParentheses(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        } 

        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                // 左括号， 进栈， 用 -1 来标识左括号
                stack.offerLast(-1);
            } else {
                // 右括号
                if (stack.peekLast() == -1) {
                    // 栈顶是左括号， 能够与之匹配 (由于s 是满足条件的，不可能栈为空)
                    stack.pollLast(); // 左括号出栈
                    // 值等于1， 入栈
                    int num = 1; // 这次计算是1
                    if (stack.size() > 0 && stack.peekLast() != -1) {
                        // 需要和之前的计算合并
                        num += stack.pollLast();
                    }
                    // 将本次计算结果入栈
                    stack.offerLast(num);
                } else {
                    // 栈顶是值
                    int num = stack.pollLast();
                    // 左右括号将值包在中间，要乘以2
                    num *= 2;
                    stack.pollLast(); // 将右括号匹配的左括号出栈
                    if (stack.size() > 0 && stack.peekLast() != -1) {
                        // 需要合并
                        num += stack.pollLast();
                    }
                    stack.offerLast(num);
                }
            }
        }

        return stack.peekLast();
    }
}
```





## 869. 重新排列得到2的幂(M)

### 题目描述

[链接](https://leetcode-cn.com/problemset/all/)

```
给定正整数 N ，我们按任何顺序（包括原始顺序）将数字重新排序，注意其前导数字不能为零。

如果我们可以通过上述方式得到 2 的幂，返回 true；否则，返回 false。

输入：61
输出：true
```

### 分析

一种思路是求得左右排列组合的方式，然后判断是否是2的幂。

还有一种更巧妙的方式是，统计词频。因为2的幂指数总共就32个， 可以枚举所有的幂指数，因为不考虑顺序，意思是，只要词频和2的幂指数的词频相等，就可以成功

### 实现

```java
class Solution {
    public boolean reorderedPowerOf2(int N) {

        int[] base = getCount(N);  // 统计词频， 统计每个数字的个数

        for (int i = 0; i < 32; i++) {
            int x = 1 << i; // 2 的 幂

            int[] temp = getCount(x); // 统计每一个 2的幂 的词频

            if (Arrays.equals(base, temp) == true) { // 词频是否是相同的
                // 组合是相等的
                return true;
            }
        }
        return false;
    }
	
    // 统计词频， 即0-9的个数
    private int[] getCount(int n) {
        int[] ans = new int[10];

        while (n > 0) {
            ans[n % 10] += 1;
            n = n / 10;
        }
        return ans;
    }
}
```





## 895. 最大频率栈(H)

### 题目描述

[链接](https://leetcode-cn.com/problems/maximum-frequency-stack/)

```
实现 FreqStack，模拟类似栈的数据结构的操作的一个类。

FreqStack 有两个函数：

push(int x)，将整数 x 推入栈中。
pop()，它移除并返回栈中出现最频繁的元素。
如果最频繁的元素不只一个，则移除并返回最接近栈顶的元素。

```

### 分析

要存储频率，先肯定需要一个`freqMap`来保存数据和个数的映射。因为设计到最大频率，和 `RFU`类似，需要一个 频率到栈的`map`。并需要保存当前的最大频率，才能直接定位到应该删除元素的地方。

### 实现

```java
class FreqStack {
    Map<Integer, Integer> freqMap;

    Map<Integer, Deque<Integer>> group;  // key 是频率， value 是对应的栈，里面保存当前频率的元素
    int maxFreq; // 最大频率

    public FreqStack() {
        freqMap = new HashMap<>();
        group = new HashMap<>();
        maxFreq = 0;
    }
    
    public void push(int x) {
        freqMap.put(x, freqMap.getOrDefault(x, 0) + 1); // 频率 加 1
        int f = freqMap.get(x);

        if (f > maxFreq) {
            // 需要更新 maxFreq
            maxFreq = f;  
        } 

        Deque<Integer> stack = group.get(f); // 得到对应的栈
        if (stack == null) {
            stack = new LinkedList<>();
        }
        stack.offerLast(x); // 将 x 压栈
        group.put(f, stack);
    }
    
    public int pop() {
        Deque<Integer> stack = group.get(maxFreq);
        int x = stack.pollLast(); // 出栈
        freqMap.put(x, freqMap.get(x) - 1); // 频率 减 1
        if (stack.size() == 0) {
            group.remove(maxFreq); // 如果 栈为空，对应的最大频率要减1， 同时删除对应的栈(也可以不删，删了应该方便gc吧)
            maxFreq -= 1;
        }
        return x;
    }
}

```





## 921. 使得括号有效的添加次数(M)

### 题目描述

[链接](https://leetcode-cn.com/problems/minimum-add-to-make-parentheses-valid/)

```
给定一个由 '(' 和 ')' 括号组成的字符串 S，我们需要添加最少的括号（ '(' 或是 ')'，可以在任何位置），以使得到的括号字符串有效。
从形式上讲，只有满足下面几点之一，括号字符串才是有效的：

它是一个空字符串，或者
它可以被写成 AB （A 与 B 连接）, 其中 A 和 B 都是有效字符串，或者
它可以被写作 (A)，其中 A 是有效字符串。
给定一个括号字符串，返回为使结果字符串有效而必须添加的最少括号数。


```

### 分析

类似于栈的思路，但这道题只用保存左括号的数量，而不用保存索引。

* 如果是左括号， 左括号个数 + 1
* 如果是右括号
  * 左括号个数不为0， 匹配一对， 左括号个数 -1
  * 否则， 需要添加一个左括号与之匹配
* 最终，剩多少左括号，也需要添加多少右括号

### 实现

```java
class Solution {
    public int minAddToMakeValid(String s) {
        int left = 0; // 左括号的多余个数
        int ans = 0; // 需要添加的次数
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            if (c == '(') {
                // 左括号， 多于个数 + 1
                left += 1;
            } else {
                if (left == 0) {
                    // 左括号不够了， 添加一个
                    ans += 1;
                } else {
                    // 否则消耗一个多于的左括号
                    left -= 1;
                }
            }
        }
        return ans + left;
    }
}
```

