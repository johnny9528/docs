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

