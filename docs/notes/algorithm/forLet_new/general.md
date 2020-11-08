# General

## 6 Z字形变换(M）

### 描述

[链接](https://leetcode-cn.com/problems/zigzag-conversion/)

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

### 分析

总共的行数为`numRows`，因此可以利用`numRows`个`StringBuilder`来存储字符(比利用数组存储节约空间)。

遍历一遍字符， 进行写入。如果当前字符对应的行数为 `i`，其中`i`大于1小于`numsRows`， 那么下一个字符写入的行数就为`i+1`或者`i-1`。因此， 统一可以用`i+flag`来表示， 顺序走的时候， `flag=1`，反之等于`flag=-1`。

### 实现

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



------



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



## 31 求下一个排列(M)

### 描述

[题目链接](https://leetcode-cn.com/problems/next-permutation/)

```
实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。
如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。
必须原地修改，只允许使用额外常数空间
示例：
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1
```

### 分析

假设数组为`1 2 3 4 7 5 3 1`， 那么下一个排列就是 `1 2 3 5 1 3 4 7`， 可以分析出， 是在4这个位置的后面找到第一个大于4的值5， 然后二者交换位置， 最后， 后面的 `7, 4, 3, 1`翻转。 因此，从后往前找到第一个不是逆序的值，就是转折点。然后顺着转折点往后找到第一个比他大的值，二者替换， 之后翻转。

需要注意的是， 如果整个数组是逆序的话， 找到的转折点的索引为 -1， 此时直接 翻转即可。

### 实现

```java
class Solution {
    public void nextPermutation(int[] nums) {
        //  1 2 3 4 7 5 3 1
        int index = nums.length - 2;
        //从后往前找到第一个非逆序的值
        while(index >= 0 && nums[index] >= nums[index + 1]) index -= 1;

        //如果index = -1 , 说明整个数组是逆序的， 直接进入最后的翻转即可
        if(index != -1) {
            int index2 = index + 1;
            找到index后大于该值的最小值
            while(index2 < nums.length && nums[index2] > nums[index]) index2++;
            index2 -= 1;

            int temp = nums[index];
            nums[index] = nums[index2];
            nums[index2] = temp;
        }
        

        //翻转index后面的数组
        int i = index + 1;
        int j = nums.length - 1;
        while(i < j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
            i++;
            j--;
        }
    }
}
```



## 41. 缺失的第一个正数

### 描述

[链接](https://leetcode-cn.com/problems/first-missing-positive/)

```
给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数
要求时间复杂度为o(n)， 空间复杂度为o(1)
输入: [3,4,-1,1]
输出: 2
```

### 分析

时间复杂度和空间复杂度的要求，进行原地哈希。遍历数组，如果某个元素，比如元素等于3，那么将第三个位置变为该位置的值的负数，持续下去，一个萝卜一个坑，如果每个坑的元素都是负数，那么缺失的第一个正数为length+1， 否则，就是第一个，正数的地方，因为没有地方和他对应。

需要注意的是，比如某个索引为1的元素值为5，会将第5个位置变为负数，但是当遍历到第五个元素的时候，需要使用正数，此时需要转为正数在去找坑位。

### 实现

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] <= 0) {
                nums[i] = nums.length + 1;
            }
        }
        for (int i = 0; i < nums.length; i++) {
            int value = Math.abs(nums[i]);
            if (value > nums.length) continue;
            int temp = nums[value - 1];
            nums[value - 1] = - Math.abs(nums[value - 1]);
        }

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > 0) return i + 1;
        }
        return nums.length + 1;
    }
}
```



## 49. 字母异位词分组

### 描述

[链接](https://leetcode-cn.com/problems/group-anagrams/)

```
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]

```

### 分析

用一个map，两个单词为异位词，对单词进行排序后(按照字母的顺序排序)相等的放到一组即可

### 实现

```java

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs.length == 0) return new ArrayList();
        Map<String, List> ans = new HashMap<String, List>();
        for (String s : strs) {
            char[] ca = s.toCharArray();
            Arrays.sort(ca);
            String key = String.valueOf(ca);
            if (!ans.containsKey(key)) ans.put(key, new ArrayList());
            ans.get(key).add(s);
        }
        return new ArrayList(ans.values());
    }
}

```



## 56. 合并区间

### 描述

[链接](https://leetcode-cn.com/problems/merge-intervals/)

```
给出一个区间的集合，请合并所有重叠的区间。

输入: intervals = [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].

```

### 分析

先对数组排序，利用comparator根据数组的第一个元素进行排序，然后就能方便观察是否重叠

### 实现

```java
class Solution {
    public int[][] merge(int[][] intervals) {

        Arrays.sort(intervals, (s1, s2) -> (s1[0] - s2[0]));
        int[][] res = new int[intervals.length][2];
        int idx = -1;
        for (int[] interval: intervals) {
            // 如果结果数组是空的，或者当前区间的起始位置 > 结果数组中最后区间的终止位置，
            // 则不合并，直接将当前区间加入结果数组。
            if (idx == -1 || interval[0] > res[idx][1]) {
                res[++idx] = interval;
            } else {
                // 反之将当前区间合并至结果数组的最后区间
                res[idx][1] = Math.max(res[idx][1], interval[1]);
            }
        }
        return Arrays.copyOf(res, idx + 1);
    }
}
```



## 54. 旋转矩阵

### 描述

[链接](https://leetcode-cn.com/problems/spiral-matrix/)

```
给定一个包含 m x n 个元素的矩阵（m 行, n 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

输入:
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]
输出: [1,2,3,4,8,12,11,10,9,5,6,7]

```

### 分析

按照层级输出，每一轮都循环四次。详情见代码

### 实现

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> ans = new LinkedList<>();
        if (matrix == null || matrix.length == 0) return ans;
        int top = 0,  bottom = matrix.length - 1;
        int left = 0, right = matrix[0].length - 1;

        int total = (right + 1) * (bottom + 1);
        while (total > 0) {
          // 注意每次循环加的限制条件  total > 0， 如果是方阵，刚好四个循环之后，total为0， 如果是普通矩阵，会有问题，所以每个循环都要加该限制条件
            for (int i = left; i <= right && total > 0; i++) {
                ans.add(matrix[top][i]);
                total -= 1;
            }
            top += 1;

            for (int i = top; i <= bottom && total > 0; i++) {
                ans.add(matrix[i][right]);
                total -= 1;
            }
            right -= 1;

            for (int i = right; i >= left && total > 0; i--) {
                ans.add(matrix[bottom][i]);
                total -= 1;
            }
            bottom -= 1;

            for (int i = bottom; i >= top && total > 0; i--) {
                ans.add(matrix[i][left]);
                total -= 1;
            }
            left += 1;
        }
        return ans;
    }
}
```



## 59. 旋转矩阵2

### 描述

[链接](https://leetcode-cn.com/problems/spiral-matrix-ii/)

```
给定一个正整数 n，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。
输入: 3
输出:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]

```



### 分析

和上一道题原理一样， 只是换种形式

### 实现

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int count = 1; 
        int left = 0, right = n - 1, top = 0, bottom = n - 1;
        int[][] matrix = new int[n][n];
        while (count <= n * n) {
          // 这道题一定是方阵， 因此可以不用加额外的限制条件
            for (int i = left; i <= right; i++) {
                matrix[top][i] = count;
                count += 1;
            }
            top += 1;

            for (int i = top; i <= bottom; i++) {
                matrix[i][right] = count;
                count += 1;
            }
            right -= 1;

            for (int i = right; i >= left; i--) {
                matrix[bottom][i] = count;
                count += 1;
            }
            bottom -= 1;

            for (int i = bottom; i >= top; i--) {
                matrix[i][left] = count;
                count += 1;
            }
            left += 1;
        }
        return matrix;
    }
}
```



## 71. 简化路径

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



## 73. 矩阵置零

### 描述

[链接](https://leetcode-cn.com/problems/set-matrix-zeroes/)

```
给定一个 m x n 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用原地算法。(原地算法即空间复杂度为o(1))
输入: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
输出: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]

```

### 分析

空间复杂度要求为常数。使用第一行和第一列来标识该行该列是否需要置为0.。 遍历整个矩阵，如果`matrix[i][j = 0`, 那么将该行的第一个元素和该列的第一个元素设为0。最后，再次遍历矩阵，如果发现该行的第一个为0.则该行都设为0， 同理，发现该列的头元素为0，该列都设为0.

但是需要注意的是，由于第一行和第一列，可能会一直变化，所以需要先判断第一行和第一列本身是否需要置为0.

### 实现

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean firstRow = false, firstCol = false;

      // 判断第一列是否需要整体置为0
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                firstCol = true;
                break;
            }
        }
      // 判断第一行整体是否需要置为0
        for (int i = 0; i < n; i++) {
            if (matrix[0][i] == 0) {
                firstRow = true;
                break;
            }
        }

      // 寻找哪些行哪些列需要整体置为0， 用头元素标识
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[0][j] = 0;
                    matrix[i][0] = 0;
                }
            }
        }

      // 替换除了第一行和第一列的其他行、列
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

      // 第一行需要置换
        if (firstRow == true) {
            for (int i = 0; i < n; i++) {
                matrix[0][i] = 0;
            }
        }
      // 第一列需要置换
        if (firstCol == true) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
    }
}
```



## 74. 搜索二维矩阵

### 描述

[链接](https://leetcode-cn.com/problems/search-a-2d-matrix/)

```java
编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。     // 剑指offer中类似题目，没有这个限制条件

输入:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
输出: true

```



### 分析

如果没有第二个限制条件，则可以设置一个baseline， 从左下角开始，如果大于target，则往右走，如果小于target，则往上走。

如果有第二个限制条件，整个矩阵展开来，扁平化之后，就是一个完全排序的数组。可以使用二分法。



### 实现

```java
// 通用的解法
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return false;
        int m = matrix.length, n = matrix[0].length;
        int row = m - 1, col = 0;
        while (row >= 0 && col < n) {
            int base = matrix[row][col];
            if (base == target) return true;
            if (target > base) {
                col += 1;
            }else {
                row -= 1;
            }
        }
        return false;
    }
}
```



## 151. 翻转字符串里的单词

### 描述

```
给定一个字符串，逐个翻转字符串中的每个单词。

输入："the sky is blue"
输出："blue is sky the"
```

### 分析

单个单词，入栈。结果整体出栈。

### 实现

```java
class Solution {
    public String reverseWords(String s) {
        int left = 0;
        int right = s.length() - 1;

        while (left <= right && s.charAt(left) == ' ') left += 1;
        while (left <= right && s.charAt(right) == ' ') right -= 1;

        StringBuilder word = new StringBuilder();
        Deque<String> deque = new LinkedList<>();
        while (left <= right) {
            char c = s.charAt(left);
            if ((word.length() != 0) && (c == ' ')) {
                // 将单词 push 到队列的头部
                deque.offerFirst(word.toString());
                word.setLength(0);
            } else if (c != ' ') {
                word.append(c);
            }
            left += 1;
        }

        deque.offerFirst(word.toString());
        return String.join(" ", deque);


    }
}
```



## 165. 比较版本号

### 描述

```
比较两个版本号 version1 和 version2。
如果 version1 > version2 返回 1，如果 version1 < version2 返回 -1， 除此之外返回 0。

你可以假设版本字符串非空，并且只包含数字和 . 字符。

 . 字符不代表小数点，而是用于分隔数字序列。

例如，2.5 不是“两个半”，也不是“差一半到三”，而是第二版中的第五个小版本。
你可以假设版本号的每一级的默认修订版号为 0。例如，版本号 3.4 的第一级（大版本）和第二级（小版本）修订号分别为 3 和 4。其第三级和第四级修订号均为 0

输入: version1 = "7.5.2.4", version2 = "7.5.3"
输出: -1
```

### 分析

切割字符串， 逐个比较即可

### 实现

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        String[] s1 = version1.split("\\.");
        String[] s2 = version2.split("\\.");
        int index = 0;
        while(index < s1.length && index < s2.length) {
            
            if(Integer.parseInt(s1[index]) > Integer.parseInt(s2[index])) {
                return 1;
            }else if(Integer.parseInt(s1[index]) < Integer.parseInt(s2[index])) {
                return -1;
            }else {
                index += 1;
            }
        }
        
        if(s1.length > s2.length) {
            for(; index < s1.length; index++) {
                if(Integer.parseInt(s1[index]) == 0) {
                    continue;
                }else {
                    return 1;
                }
            }
        }
        if(s1.length < s2.length) {
            for(; index < s2.length; index++) {
                if(Integer.parseInt(s2[index]) == 0) {
                    continue;
                }else {
                    return -1;
                }
            }
        }
        
        return 0;
    }
}
```



## 168. excle表列名称

### 描述

```
给定一个正整数，返回它在 Excel 表中相对应的列名称
    1 -> A
    2 -> B
    3 -> C
    ...
    26 -> Z
    27 -> AA
    28 -> AB 
    ...

```

### 分析

余数和除数的简单运算

### 实现

```java
class Solution {
    public String convertToTitle(int x) {
        StringBuilder sb = new StringBuilder();

        while (x > 0) {
            x -= 1;
            int extra = x % 26;
            sb.append((char)('A' + (extra)));
            x = x / 26;
        }
        return sb.reverse().toString();
    }
}
```



## 169. 多数元素

### 描述

[链接](https://leetcode-cn.com/problems/majority-element/)

```
给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。
你可以假设数组是非空的，并且给定的数组总是存在多数元素。

输入: [2,2,1,1,1,2,2]
输出: 2

```

### 分析

哈希表统计个数，思路很简单

* 摩尔投票？  有点迷幻

### 实现

```java
/**
哈希表
**/
class Solution {
    public int majorityElement(int[] nums) {
        if(nums.length == 1) return nums[0];
        Map<Integer, Integer> map = new HashMap<>();
		int base = nums.length / 2;
		for(int i = 0; i < nums.length; i++) {
			if(map.get(nums[i]) == null) {
				map.put(nums[i], 1);
			}else if(map.get(nums[i]) > base - 1) {
				return nums[i];
			}else {
				map.put(nums[i],map.get(nums[i]) + 1);
			}
		}
		return -1;
    }
}


/**
  摩尔投票
**/
class Solution {
    public int majorityElement(int[] nums) {
        Integer ans = null;
        int count = 0;

        for (int num : nums) {
            if (count == 0) {
                ans = num;
            }
            if (num == ans) {
                count += 1;
            } else {
                count -= 1;
            }
        }
        return ans;
    }
}
```



## 172. 阶乘后的零
### 描述
```
给定一个整数 n，返回 n! 结果尾数中零的数量。

输入: 5
输出: 1
解释: 5! = 120, 尾数中有 1 个零.
```
### 分析
0的个数，5 * 2 = 10,则会有个0.有多少个5的倍数，就有多少个10.即有多少个0（2的倍数一定会大于5的倍数）
### 实现
```java
class Solution {
    public int trailingZeroes(int n) {
        int count = 0;
        for (int i = 1; i <= n; i++) {
            int num = i;
            while (num % 5 == 0) {
                count += 1;
                num = num / 5;
            }
        }
        return count;
    }
}
```