# 其他问题

```
229     315  331  334
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

