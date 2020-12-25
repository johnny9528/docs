# 其他问题

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





