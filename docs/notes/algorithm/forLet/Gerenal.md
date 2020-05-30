# Gerenal

## Q_6 Z字形变换(M）

### 1.1. 描述

[题目链接](https://leetcode-cn.com/problems/zigzag-conversion/)

```
示例1：
输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"
解释：
L   C   I   R
E T O E S I I G
E   D   H   N

示例2：
输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:
L     D     R
E   O E   I I
E C   I H   N
T     S     G
```

### 1.2. 分析

总共的行数为`numRows`，因此可以利用`numRows`个`StringBuilder`来存储字符(比利用数组存储节约空间)。

遍历一遍字符， 进行写入。如果当前字符对应的行数为 `i`，其中`i`大于1小于`numsRows`， 那么下一个字符写入的行数就为`i+1`或者`i-1`。因此， 统一可以用`i+flag`来表示， 顺序走的时候， `flag=1`，反之等于`flag=-1`。

### 1.3. 实现

```java
class Solution {
    public String convert(String s, int numRows) {
        if(numRows == 1) return s;
        List<StringBuilder> list = new ArrayList<>(numRows);
        for(int i = 0; i < numRows; i++) {
            list.add(new StringBuilder());
        }
        int flag = -1;
        int index = 0;
        for(int i = 0; i < s.length(); i++) {
            list.get(index).append(s.charAt(i));
            if(index == 0 || index == numRows - 1) {
                flag = -flag;
            } 
            index += flag;
        }

        StringBuilder ans = new StringBuilder();
        for(StringBuilder each : list) {
            ans.append(each);
        }
        return ans.toString();

    }
}
```



## Q_9 回文数(E）

### 描述

[题目链接](https://leetcode-cn.com/problems/palindrome-number/)

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





