# 剑指 offer



## 3. 数组中重复的数字

### 题目描述

[链接](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

```
找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

### 分析

因为长度为n， 所有数字都在 0-n-1中，因此，如果没有数字重复，基本上一个萝卜一个坑.从头开始，如果值和索引不相等，就一直找下去。

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        for(int i = 0; i < nums.length; i++) {
            while(nums[i] != i) {
                if(nums[i] == nums[nums[i]]) return nums[i];
                int temp = nums[i];
                nums[i] = nums[temp];
                nums[temp] = temp;
            }
        }
        return -1;
    }
}
```



## 4. 二维数组查找值

### 题目描述

[链接](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

```
在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
```

### 分析

第三次分析了，从左下角开始

### 实现

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix.length == 0 || matrix[0].length == 0) return false;
        int col = 0;
        int row = matrix.length - 1;
        while(col < matrix[0].length && row >= 0) {
            if(matrix[row][col] > target) {
                row -= 1;
            }else if(matrix[row][col] < target) {
                col += 1;
            }else {
                return true;
            }
        }
        return false;
    }
}
```



## 6. 从尾到头打印链表

### 描述

[链接](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

```
输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。
输入：head = [1,3,2]
输出：[2,3,1]
```

### 分析

数组返回，必须要知道总的长度。可以使用栈，所有节点压栈，然后得到结果。也可以先遍历一次得到长度，然后再遍历一次得到结果

### 实现

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        int length = 0;
        ListNode temp = head;
        while (temp != null) {
            length += 1;
            temp = temp.next;
        }

        int[] ans = new int[length];
        int index = length - 1;
        while (head != null) {
            ans[index] = head.val;
            head = head.next;
            index -= 1;
        }
        return ans;
    }
}
```



## 6. 根据前序和中序重建二叉树

### 描述

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
    3
   / \
  9  20
    /  \
   15   7
```

### 实现

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        return buileSubTree(preorder, inorder, 0, preorder.length - 1, 0, inorder.length - 1);
    }
    public TreeNode buileSubTree(int[] pre, int[] ino, int i, int j, int m, int n) {
        if(i > j) return null;
        TreeNode root = new TreeNode(pre[i]);
        if(i == j) return root;
        int index = m;
        while(ino[index] != pre[i]) index += 1;
        root.left = buileSubTree(pre, ino, i + 1, i + index - m, m, index - 1);
        root.right = buileSubTree(pre, ino, i + index - m + 1, j, index + 1, n);
        return root;
    } 
}
```



## 10-1. 斐波那契数列

### 描述

[链接](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

```
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。
答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

输入：n = 5
输出：5
```

### 分析

简单动态规划

### 实现

```java
class Solution {
    public int fib(int n) {
        if (n < 2) return n;
        int first = 0;
        int second = 1;
        for (int i = 2; i <= n; i++) {
            int temp = first + second;
            first = second;
            second = temp % 1000000007;
        }
        return second;
    }
}
```



## 10-2. 青蛙跳台阶问题

### 题目描述

```
青蛙能跳一步或者两步， 求总的方式
```

### 分析

简单dp，和上题一样

### 实现

```java
class Solution {
    public int numWays(int n) {
        if (n <= 2) {
            return n == 0 ? 1 : n;
        }

        int first = 1;
        int second = 2;
        int temp;
        for (int i = 3; i <= n; i++) {
            temp = second;
            second = (second + first) % 1000000007;
            first = temp;
        }
        return second;
    }
}
```



## 11. 旋转数组的最小数字

### 描述

[链接](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

```
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。
输入：[3,4,5,1,2]
输出：1
```

### 分析

因为数组旋转，通过两个端找到中间，那么有一边一定是排好序的。会存在下面三种情况：

* `nums[mid] < nums[right]` ， 中间值比右侧小，那么mid右侧一定是排好序的，右侧都是大于nums[mid]的，因此最小值一定在mid的左侧(可能就是mid)
* `nums[mid] > nums[right]` 这种情况下，左侧一定是排好序的，但是由于有翻转， 最小值一定在mid右侧
* 二者相等，不能得出什么结论，唯一结论是，最小值不是right，因为mid和right一样大。

### 实现

```java
class Solution {
    public int minArray(int[] numbers) {
        int low = 0;
        int high = numbers.length - 1;
        while(low < high) {
            int middle = low + (high - low) / 2;
            if(numbers[middle] > numbers[high]) {
                low = middle + 1;
            }else if(numbers[middle] < numbers[high]) {
                high = middle;
            }else {
                high -= 1;
            }
        }
        return numbers[low];
    }
}
```



## 12. 矩阵中的路径

### 题目描述

[链接](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

```
请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。

[["a","b","c","e"],
["s","f","c","s"],
["a","d","e","e"]]
但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

### 分析

回溯算法。针对某一个起点，采用深度优先遍历寻找结果，找到就返回true，停止寻找，否则继续找。

由于不能重复进入格子，需要一个used数组记录已经到达过的地方

### 实现

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        boolean[][] used = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (backTrack(board, i, j, word, 0, used)) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean backTrack(char[][] board, int i, int j, String word, int index, boolean[][] used) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) {
            // 超出边界， 返回false
            return false;
        }
        if (used[i][j] == true) {
            // 这个格子已经被用过了
            return false;
        }
        if (board[i][j] != word.charAt(index)) {
            // 当前的字符不等于对应的字符，不继续了，直接返回false
            return false;
        }
        if (index == word.length() - 1) {
            // ok, 找到一个正确的了
            return true;
        }

        used[i][j] = true; // 标识当前格子被使用了
		
        // 向四个方向遍历，  || 有一个是true， 就会直接返回true
        boolean isTrue = backTrack(board, i - 1, j, word, index + 1, used) || backTrack(board, i + 1, j, word, index + 1, used) ||
                backTrack(board, i, j - 1, word, index + 1, used) || backTrack(board, i, j + 1, word, index + 1, used);

        if (isTrue == false) {
            // 如果 i, j 为起点找不到正确结果，需要将used设置回初始状态
            used[i][j] = false;
        }
        return isTrue;    
    }
}
```



## 13。 机器人的运动范围

### 描述

[链接](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

```
地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？
输入：m = 2, n = 3, k = 1
输出：3
```

### 分析

回溯， 但是本题隐藏的优化点是，只用计算向右和向下的值即可。

### 实现

```java
class Solution {
    public int movingCount(int m, int n, int k) {
        int[][] visited = new int[m][n];
        return helper(m, n, 0, 0, k, visited);
    }
    public int helper(int m, int n, int i, int j, int k, int[][] visited) {
        if(i < 0 || i >= m || j < 0 || j >= n || visited[i][j] == 1) return 0;
        int sum = getSumByBit(i) + getSumByBit(j);
        if(sum > k) return 0;
        visited[i][j] = 1;
        return helper(m, n, i + 1, j, k, visited) + helper(m, n, i, j + 1, k, visited) + 1;
    }
    public int getSumByBit(int x) {
        int sum = 0;
        while(x != 0) {
            sum += x % 10;
            x = x / 10;
        }
        return sum;
    }
}
```



## 14. 减绳子

### 描述

[链接](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

```
给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
```

### 分析

比如说， 绳子长度为20， 那么划分方式有：

* 减去1， 剩下19， 那么19要么不减了， 乘积为 19, 要么19继续划分， 乘积为 1 * (19划分的结果)
* 减去2， 剩下18， 18要么不继续划分了，乘积为 36， 要么为 2 * (18划分的结果)
* ...

因此这是一个典型的递归树， 递归树可以用空间换时间

采用dp

### 实现

```java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];

        for (int i = 2; i <= n; i++) {
            for (int j = 1; j < i; j++) {
                dp[i] = Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j]));
            }
        }
        return dp[n];
    }
}
```



## 15. 二进制中1的个数

### 描述

[链接](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

```
请实现一个函数，输入一个整数（以二进制串形式），输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。

```

### 分析

一直往左移动， 遇到1就加1

### 实现

```java
public class Solution {
    public int hammingWeight(int n) {
        int count = 0;
        for (int i = 0; i < 32; i++) {
            if (((1 << i) & n) != 0) {
                // 该位是1
                count += 1;
            }
        }
        return count;
    }
}
```



## 16. 数值的整数次方

### 描述

[链接](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

```
实现函数double Power(double base, int exponent)，求base的exponent次方。不得使用库函数，同时不需要考虑大数问题。

输入: 2.00000, 10
输出: 1024.00000
```

### 分析

典型的分治， 比如 1024次方，如果得出512的结果了，直接两个乘起来就可以

### 实现

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) {
            return 1.0;
        }
        if (n < 0) {
            // 为了防止负数的时候整形溢出， 先拿出一个来
            return calcu(1.0 / x, -n - 1) * (1.0 / x);
        }
        return calcu(x, n);
    }

    private double calcu(double x, long n) {
        if (n == 0) {
            return 1;
        }
        if (n == 1l) {
            return x;
        }
        if ((n & 1) == 0) {
            // 偶数
            double subAns = calcu(x, n >> 1);
            return subAns * subAns;
        } else {
            double subAns = calcu(x, n >> 1);
            return subAns * subAns * x;
        }
    }
}
```





## 18. 删除链表中的某个节点

### 描述

[链接](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

```
给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。
返回删除后的链表的头节点。

输入: head = [4,5,1,9], val = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

### 分析

遍历链表，当需要删除的时候，前驱节点指向该节点的下一个节点即可

### 实现

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        ListNode preHead = new ListNode(); // 前驱节点
        ListNode preHeadDumpy = preHead;

        while (head != null) {
            if (head.val != val) {
                // 还没到需要删除的地方
                ListNode next = head.next;
                preHead.next = head;
                preHead = preHead.next;
                head = next;
            } else {
                // 此时， 应该删除该节点，前驱节点直接指向 current的下一个节点即可，注意break
                preHead.next = head.next;
                break;
            }
        }
        return preHeadDumpy.next;
    }
}
```



## 21. 调整数组顺序使得奇数在左边，偶数在右边

### 描述

[链接](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

```
输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。
```

### 分析

双指针，遇到都不满足的时候，换。有点像快排的思路

### 实现

```java
class Solution {
    public int[] exchange(int[] nums) {
        int left = 0;
        int right = nums.length - 1;

        while (left < right) {
            if (nums[left] % 2 == 0 && nums[right] % 2 == 1) {
                // 左边是偶数，右边是奇数，需要换
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                left += 1;
                right -= 1;
            } else if (nums[left] % 2 == 1 && nums[right] % 2 == 1) {
                // 左右都是奇数， 右边不动，等着换
                left += 1;
            } else if (nums[left] % 2 == 0 && nums[right] % 2 == 0) {
                // 左右都是偶数， 左边不动，等着换
                right -= 1;
            } else {
                // 左边奇数， 右边偶数， ok,没问题
                left += 1;
                right -= 1;
            }
        }
        return nums;
    }
}
```



## 22. 链表中的倒数第个节点

### 描述

[链接](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

```
输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。例如，一个链表有6个节点，从头节点开始，它们的值依次是1、2、3、4、5、6。这个链表的倒数第3个节点是值为4的节点。

给定一个链表: 1->2->3->4->5, 和 k = 2.
返回链表 4->5.
```

### 分析

双指针，快指针，先走k步，然后慢指针从头出发，一起走，快指针走到尽头的时候，慢指针走到了倒数第K个节点

### 实现

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head;
        for (int i = 1; i <= k; i++) {
            if (fast == null) {
                return null;
            }
            fast = fast.next;
        }

        while (fast != null) {
            fast = fast.next;
            head = head.next;
        }

        return head;
    }
}
```



## 24. 反转单链表

### 实现

```java
/**
	递归
**/
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode subTail = head.next;  // 下一个节点是递归之后的尾巴

        ListNode subAns = reverseList(head.next);
        subTail.next = head;  // 更新尾巴，之前的尾巴指向现在节点
        head.next = null; // 现在的节点成为尾巴，需要指向null

        return subAns;
    }
}

/**
	迭代
**/
class Solution {
    public ListNode reverseList(ListNode head) {

        ListNode subHead = null;  // 已经反转完成的头节点
        while (head != null) {
            ListNode next  = head.next;
            head.next = subHead; // head成为新的头节点，指向之前的头节点
            subHead = head;
            head = next;
        }

        return subHead;
    }
}
```



## 25. 合并排序链表

### 实现

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode preHead = new ListNode();
        ListNode temp = preHead;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                temp.next = l1;
                temp = temp.next;
                l1 = l1.next;
            } else {
                temp.next = l2;
                temp = temp.next;
                l2 = l2.next;
            }
        }

        if (l1 != null) {
            temp.next = l1;
        } else {
            temp.next = l2;
        }
        return preHead.next;
    }
}
```



## 26. 树的子结构

### 描述

[链接](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

```
输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

给定的树 A:

     3
    / \
   4   5
  / \
 1   2
给定的树 B：

   4 
  /
 1
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。
```

### 分析

如果确定了第一个开始的节点，那么就类似于判断两个树是否相同来判断了。简单递归

这道题并不能进行优化

### 实现

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if(B == null || A == null) return false;
        return helper(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
    }
    public boolean helper(TreeNode A, TreeNode B) {
        if(B == null) return true;
        if(A == null || A.val != B.val) return false;
        return helper(A.left, B.left) && helper(A.right, B.right);
    }
}
```



## 27. 二叉树镜像

### 描述

[链接](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

```
请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9
镜像输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

### 实现

```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        
        TreeNode left = root.left;
        root.left = root.right;
        root.right = left;
        mirrorTree(root.left);
        mirrorTree(root.right);
        return root;
    }
}
```



## 28. 对称的二叉树

### 题目描述

[链接](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/submissions/)

```
请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

    1
   / \
  2   2
 / \ / \
3  4 4  3

```

### 实现

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return root == null || helper(root.left, root.right);
    }
    public boolean helper(TreeNode root1, TreeNode root2) {
        if(root1 == null && root2 == null) return true;
        if(root1 == null || root2 == null || root1.val != root2.val) return false;
        return helper(root1.left, root2.right) && helper(root1.right, root2.left);
    }

```



## 29. 顺时针打印矩阵

### 描述

[链接](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

```
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

### 分析

模拟这个过程即可

需要注意的是，如果矩阵不是方针，循环一圈内部也需要判断是否需要停止

### 实现

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0) {
            return new int[] {};
        }
        int index = 0;
        int left = 0;
        int top = 0;
        int bottom = matrix.length - 1; 
        int right = matrix[0].length - 1;
        int[] ans = new int[(bottom + 1) * (right + 1)];

        while (left <= right && top <= bottom) {
            for (int j = left; j <= right; j++) {
                ans[index] = matrix[top][j];
                index += 1;
            }
            top += 1;
            for (int i = top; i <= bottom; i++) {
                ans[index] = matrix[i][right];
                index += 1;
            }
            right -= 1;

            // 执行了一半之后，此处需要判断一次，因为如果不是方针，可能就已经遍历完所有的了
            if (left > right || top > bottom) {
                break;
            }

            for (int j = right; j >= left; j--) {
                ans[index] = matrix[bottom][j];
                index += 1;
            }
            bottom -= 1;
            for (int i = bottom; i >= top; i--) {
                ans[index] = matrix[i][left];
                index += 1;
            }
            left += 1;
        }
        return ans;
    }
}
```



## 32. 从上到下遍历树

### 描述

```
  3
   / \
  9  20
    /  \
   15   7
[3,9,20,15,7]
```

### 分析

层序遍历，bfs

### 实现

```java
class Solution {
    public int[] levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        List<Integer> list = new ArrayList<>();
        while(queue.size() > 0) {
            TreeNode topNode = queue.poll();
            if(topNode == null) continue;
            list.add(topNode.val);
            queue.add(topNode.left);
            queue.add(topNode.right);
        }
        int[] ans = new int[list.size()];
        for(int i = 0; i < list.size(); i++) {
            ans[i] = list.get(i);
        }
        return ans;
    }
}
```



## 32-3. 从上到下打印树3

### 描述

```
请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

[
  [3],
  [20,9],
  [15,7]
]
```

### 分析

采用双向链表实现层序遍历即可

### 实现

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) {
            return ans;
        }

        Deque<TreeNode> deque = new LinkedList<>();
        deque.addLast(root);
        int level = 1;

        while (deque.isEmpty() == false) {
            int count = deque.size();
            List<Integer> list = new ArrayList<>(count);
            if (level % 2 == 1) {
                // 奇数层
                for (int i = 1; i <= count; i++) {
                    TreeNode first = deque.removeFirst();
                    list.add(first.val);
                    if (first.left != null) {
                        deque.addLast(first.left);
                    }
                    if (first.right != null) {
                        deque.addLast(first.right);
                    }
                }
            } else {
                for (int i = 1; i <= count; i++) {
                    TreeNode first = deque.removeLast();
                    list.add(first.val);
                    if (first.right != null) {
                        deque.addFirst(first.right);
                    }
                    if (first.left != null) {
                        deque.addFirst(first.left);
                    }
                }
            }
            ans.add(list);
            level += 1;
        }
        return ans;
    }
}
```



## 52. 两个链表的第一个公共节点

### 描述

[链接](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

```
输入两个链表，找出它们的第一个公共节点。
```

### 分析

两个指针，一个先走完A， 一个先走完B， 然后一个走B， 一个走A， 相遇的时候，就是结果

### 实现

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) return null;
        
        ListNode first = headA;
        ListNode second = headB;
        while (first != null && second != null) {
            if (first == second) return first;
            first = first.next;
            second = second.next;
        }
        // 有一个已经走完一圈
        if (first == null) {
            first = headB;
            while (first != null) {
                if (second == null) {
                    second = headA;
                }
                if (first == second) return first;
                first = first.next;
                second = second.next;
            }
        } else if (second == null) {
            second = headA;
            while (second != null) {
                if (first == null) {
                    first = headB;
                }
                if (first == second) return first;
                first = first.next;
                second = second.next;
            }
        }

        return null;
    }
}
```



## 33. 二叉搜索树的后序遍历序列是否正确

### 描述

[链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

```
输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 true，否则返回 false。假设输入的数组的任意两个数字都互不相同。
参考这个二叉树

  	5
    / \
   2   6
  / \
 1   3
 
 输入: [1,3,2,6,5]
输出: true
```

### 分析

如果一个数组是二叉搜索树的后序遍历。需要满足以下几个：

* 数组的最后一个数字是根节点。
* 从左往右，找到第一个大于根节点的元素，那么这个元素后面的元素都必须大于根节点。
* 具体而言，从左往右扫描，如果第一次找到大于5的元素，6，那么6之后的元素必须都大于5.比如 `[1,2, 3, 6,4,5]`就是错的
* 因此，可以从左往右找到第一个大于根节点的元素，从又往左找到第一个小于根节点的元素，如果这两个元素是挨着的，继续递归判断子树，否则直接返回false

### 实现

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        return isRight(postorder, 0, postorder.length - 1);
    }

    private boolean isRight(int[] nums, int left, int right) {
        if (left >= right) {
            return true;
        }

        // 寻找index1 是第一个大于根节点元素的索引
        int index1 = left;
        while (nums[index1] < nums[right]) {
            index1 += 1;
        }

        // index1 是第一个大于right的索引

        int index2 = right - 1;
        while (index2 >= left && nums[index2] > nums[right]) {
            index2 -= 1;
        }
        // Index2是最后1个小于right的索引
        
        if (index1 != index2 + 1) {
            // 如果要正确，index1和index2必须是挨着的
            return false;
        }
		
        // 递归判断子树是否正确
        return isRight(nums, left, index2) && isRight(nums, index1, right - 1);

    }
}
```



## 34. 二叉树中路径和为某个值的路径

### 题目描述

[链接](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

```
输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径
给定如下二叉树，以及目标和 sum = 22，
    		  5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1

[
   [5,4,11,2],
   [5,8,4,5]
]
```

### 分析

题目限定了，是根节点到叶子节点的路径(如果要求路径的结果集，不加这个限制条件，没法做，如果只求路径个数，可以不加这个限制条件)。

回溯

* 遇到一个节点，把该节点值加入到路径
* 判断当前节点是否是根节点
  * 是根节点，看路径和是否满足，满足则加入不满足则继续
  * 不是根节点，往左走，往右走
* 子树都已经走完，将当前节点从路径中删除，回溯
* 由于二叉树天然的往下的顺序，不用used数组

### 实现

```java
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> ans = new ArrayList<>();
        Deque<Integer> path = new LinkedList<>();
        backTrack(root, ans, path, sum, 0);
        return ans;
    }

    private void backTrack(TreeNode root, List<List<Integer>> ans, Deque<Integer> path, int sum, int preSum) {
        if (root == null) {
            return;
        }
        path.addLast(root.val);
        preSum += root.val;

        if (root.left == null && root.right == null) {
            // 是叶子节点
            if (preSum == sum) {
                ans.add(new ArrayList(path));
            }
        } else {
            backTrack(root.left, ans, path, sum, preSum);
            backTrack(root.right, ans, path, sum, preSum);
        }

        path.removeLast();
    }
}
```



## 35. 复杂链表的复制

### 题目描述

[链接](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

```
请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
(题目不好描述，点开原题查看)
```

### 分析

直接深度优先遍历即可，但是可能存在环， 因此需要一个标记，哪些已经构造过了。

### 实现

```java
class Solution {
    public Node copyRandomList(Node head) {
        Map<Node, Node> used = new HashMap<>();
        return dfs(head, used);
    }

    private Node dfs(Node head, Map<Node, Node> used) {
        if (head == null) {
            return null;
        }
        if (used.containsKey(head)) {
            //已经构造过了, 直接返回结果
            return used.get(head);
        }


        Node createdNode = new Node(head.val); // 复制该节点
        used.put(head, createdNode);  // 加入集合中，后面遇到这个节点，直接返回

        createdNode.next = dfs(head.next, used); // 构造他的下一个节点
        createdNode.random = dfs(head.random, used); // 构造他的random节点
        return createdNode;

    }
}
```



## 36. 二叉搜索树转为循环双向链表

### 题目描述

[链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

```
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。
```

### 分析

跟转化成单链表类似，得到当前节点的左子树的头部和尾巴，以及右子树的头部和尾巴，然后首尾首尾相连即可

### 实现

```java
class Solution {
    public Node treeToDoublyList(Node root) {
        Node[] ans = getHeadAndTail(root);
        if (ans[0] == null || ans[1] == null) {
            return ans[0];
        }
        
        // 因为是循环链表，需要将整体的首尾相连
        ans[1].right = ans[0];
        ans[0].left = ans[1];
        return ans[0];
    }
	
    // 得到当前树构造后的头部和尾巴
    private Node[] getHeadAndTail(Node root) {
        if (root == null) {
            return new Node[] {null, null};
        }

        Node[] ans = new Node[2];

        Node[] leftAns = getHeadAndTail(root.left);
        Node[] rightAns = getHeadAndTail(root.right);

        root.left = null;
        root.right = null;

        if (leftAns[0] == null && rightAns[0] == null) {
            // 意味着 左子树和右子树都为空，头部和尾巴都是root
            ans[0] = root;
            ans[1] = root;
            return ans;
        }

        if (leftAns[0] == null) {
            // 左子树为空
            ans[0] = root; // 头部是root
            root.right = rightAns[0];
            rightAns[0].left = root;
            ans[1] = rightAns[1]; // 尾巴是右子树的尾巴
            return ans;
        } 

        if (rightAns[0] == null) {
            // 右子树为空
            ans[0] = leftAns[0]; // 头部是左边的头部
            leftAns[1].right = root;
            root.left = leftAns[1];
            ans[1] = root; // 尾巴是root
            return ans;
        }

        // 左边和右边都不为空
        ans[0] = leftAns[0]; // 头部是左边的头部
        leftAns[1].right = root;
        root.left = leftAns[1];
        root.right = rightAns[0];
        rightAns[0].left = root;
        ans[1] = rightAns[1]; // 尾巴是右边的尾巴

        return ans;

    }
}
```



## 38. 字符串的排列

### 描述

[链接](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

```
输入一个字符串，打印出该字符串中字符的所有排列。
你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

输入：s = "abc"
输出：["abc","acb","bac","bca","cab","cba"]
```

### 分析

我分析累了，经典的可能存在重复的回溯，先排序，再回溯

### 实现

```java
class Solution {
    public String[] permutation(String s) {
        char[] string = s.toCharArray();
        // 排序
        Arrays.sort(string);

        List<String> ans = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        boolean[] used = new boolean[s.length()];

        backTrade(string, ans, sb, used);
        return ans.toArray(new String[ans.size()]);
    }

    private void backTrade(char[] charArray, List<String> ans, StringBuilder sb, boolean[] used) {
        if (sb.length() == charArray.length) {
            // 得到一个结果
            ans.add(sb.toString());
            return;
        }

        for (int i = 0; i < charArray.length; i++) {
            if (used[i] == true) {
                // 已经被使用了
                continue;
            }

            if (i > 0 && charArray[i] == charArray[i - 1] && used[i - 1] == false) {
                //   aabc这种字符串如果已经找了 abc(第一个a)， 再找abc(第二个a)， 会存在重复，去掉这种重复
                continue;
            }

            sb.append(charArray[i]); // 加入path
            used[i] = true; // 标识被使用了
            backTrade(charArray, ans, sb, used); // 找下一个
            sb.deleteCharAt(sb.length() - 1); // 回溯
            used[i] = false; // 标识为没有被使用
        }
    }
}
```



## 39. 数组中超过一半的元素

### 描述

[链接](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)

```
数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。
你可以假设数组是非空的，并且给定的数组总是存在多数元素。

输入: [1, 2, 3, 2, 2, 2, 5, 4, 2]
输出: 2
```

### 分析

投票法， 不一定存在还需要验证.

投票， 如果是当前候选人，票数加1， 如果不是，票数减一。最后还的票的一定是多于半数的

### 实现

```java
class Solution {
    public int majorityElement(int[] nums) {
        int vote = 0;
        int base = 0;
        for(int num : nums) {
            if(vote == 0) base = num;
            if(num == base) {
                vote += 1;
            }else {
                vote -= 1;
            }
            
        }
        return base;
    }
}
```



## 40. 最小的K个数

### 描述

[链接](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

```
输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```

### 分析

一趟快排之后，会找到主元的索引， 这个key将数组划分为两部分，左边小于key， 右边大于key。

如果key， 等于k，直接返回。如果key大于k, 在key左边继续一趟快排， 反之

### 实现

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        quickSortOnce(arr, 0, arr.length - 1, k);

        int[] ans = new int[k];
        for (int i = 0; i < k; i++) {
            ans[i] = arr[i];
        }

        return ans;
    }
	
    // 一趟快排， 注意是一趟，不是一次
    private void quickSortOnce(int[] nums, int startIndex, int endIndex, int k) {
        if (startIndex >= endIndex) {
            return;
        }

        int key = nums[startIndex];
        int left = startIndex;
        int right = endIndex;

        while (left < right) {

            while (left < right && nums[right] >= key) {
                right -= 1;
            }
            swap(nums, left, right);

            while (left < right && nums[left] <= key) {
                left += 1;
            }
            swap(nums, left, right);
        }
        
        // 一趟快排结束，left是主元的位置

        if (left == k) {
            // ok， 找到了，返回
            return;
        } else if (left > k) {
            // 在左边找
            quickSortOnce(nums, startIndex, left - 1, k);
        } else {
            // 在右边继续找
            quickSortOnce(nums, left + 1, endIndex, k);
        }
    } 

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```



## 42. 连续子数字的最大和

### 描述

[链接](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

```
输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。
要求时间复杂度为O(n)。

输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

### 分析

简单dp。`dp[i]` 是子数组最后一个元素是`nums[i]`的最大和。那么有

* `dp[i - 1] > 0`  ，更新`dp[i] = dp[i - 1] + nums[i]`
* 反之，`dp[i - 1]`对应 i来说是个累赘，直接不要`dp[i] = nums[i]`
* 然后找最大值
* 可以优化空间复杂度，但是处于好理解，就别优化了吧

### 实现

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        int len = nums.length;
        int[] dp = new int[len];

        dp[0] = nums[0];
        int ans = dp[0];
        for (int i = 1; i < len; i++) {
            if (dp[i - 1] < 0) {
                dp[i] = nums[i];
            } else {
                dp[i] = nums[i] + dp[i - 1];
            }

            ans = Math.max(ans, dp[i]);
        }

        return ans;

    }
}
```



### 43. 1-n中1的个数(H)

### 描述

[链接](https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

```
输入一个整数 n ，求1～n这n个整数的十进制表示中1出现的次数。
例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。

输入：n = 12
输出：5
```

### 分析

找规律进行递归的题，建议记住即可，代码很简单

f(n))函数的意思是1～n这n个整数的十进制表示中1出现的次数，将n拆分为两部分，最高一位的数字high和其他位的数字last，分别判断情况后将结果相加

例子如n=1234，high=1, pow=1000, last=234

可以将数字范围分成两部分1~999和1000~1234

1~999这个范围1的个数是f(pow-1)
1000~1234这个范围1的个数需要分为两部分：

* 千分位是1的个数：千分位为1的个数刚好就是234+1(last+1)，注意，这儿只看千分位，不看其他位
  其他位是1的个数：即是234中出现1的个数，为f(last)
  所以全部加起来是f(pow-1) + last + 1 + f(last);

例子如3234，high=3, pow=1000, last=234

可以将数字范围分成两部分1~999，1000~1999，2000~2999和3000~3234

1~999这个范围1的个数是f(pow-1)
1000~1999这个范围1的个数需要分为两部分：

* 千分位是1的个数：千分位为1的个数刚好就是pow，注意，这儿只看千分位，不看其他位
  其他位是1的个数：即是999中出现1的个数，为f(pow-1)
  2000~2999这个范围1的个数是f(pow-1)
  3000~3234这个范围1的个数是f(last)
  所以全部加起来是pow + high*f(pow-1) + f(last);

### 实现

```java
class Solution {
    public int countDigitOne(int n) {
        if(n <= 0) return 0;
        if(n <= 9) return 1;
        String s = "" + n;
        int high = s.charAt(0) - '0';
        int pow = (int)Math.pow(10, s.length() - 1);
        int last = n - high * pow;
        if (high == 1) {
            return countDigitOne(pow-1) + last + 1 + countDigitOne(last);
        } else {
            return pow + high*countDigitOne(pow-1) + countDigitOne(last);
        }

    }
}
```



## 44. 数字序列中某一位的数字

### 描述

[链接](https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

```
数字以0123456789101112131415…的格式序列化到一个字符序列中。在这个序列中，第5位（从下标0开始计数）是5，第13位是1，第19位是4，等等。

请写一个函数，求任意第n位对应的数字。

输入：n = 11
输出：0
```

### 分析

第一步，先定位在哪个区间， 比如 10-100， 100-1000， 1000-10000.

0-9之间每个数字，占一个位子，10-99，每一个占两个，后面同理。

比如11定位到10-99之间，11 减去9个位置，还剩两个，即11在10-99中的第2个位置，每个数字占两个位置，即11是数字10的后面一个，即0

### 实现

```java
class Solution {
    public int findNthDigit(int n) {
        int base = 1;
        long count = 9l;
        long start = 1;
        while(n > count) {
            n -= count;
            start *= 10;
            base += 1;
            count = start * 9 * base; 
        }

        // System.out.println("the is " + n);
        long num = (n - 1) / base + start;
        int extra = (n - 1) % base;
        return Long.toString(num).charAt(extra) - '0';

    }
}
```



## 45. 把数组排成最小的数

### 题目描述

[链接](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

```
输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

输入: [10,2]
输出: "102"

输入: [3,30,34,5,9]
输出: "3033459"
```

### 分析

自定义一个排序， 因为是字符串的拼接， x和y拼接有两种可能， x + y 和 y + x，隐因此只需要利用这个规则排序即可

### 实现

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = "" + nums[i];
        }
        Arrays.sort(strs, (x, y) -> {
            return (x + y).compareTo(y + x);
        });

        // System.out.println(Arrays.toString(strs));
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s);
        }
        return sb.toString();
    }
}
```



## 46.把数字翻译成字符串

### 题目描述

[链接](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

```
给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。
输入: 12258
输出: 5
解释: 12258有5种不同的翻译，分别是"bccfi", "bwfi", "bczi", "mcfi"和"mzi"
```

### 分析

典型dp，可能需要注意的是0的影响。`dp[i]` 是截至当前字符的翻译方式的个数。那么有下面几种情况：

* i位置的字符，自己单独翻译，那么`dp[i] = dp[i - 1]`
* i位置字符和i-1之间字符一起翻译
  * 位置i-1的字符是0， 不能一起翻译,比如 02
  * 否则，两个数字的值大于26不能一起翻译， 比如32
  * 其余情况， `dp[i] += dp[i - 2]`

### 实现

```java
class Solution {
    public int translateNum(int num) {
        String s = Integer.toString(num);
        if(s.length() <= 1) return s.length();
        int[] dp = new int[s.length() + 1];
        dp[0] = 1;dp[1] = 1;
        for(int i = 2; i <= s.length(); i++) {
            dp[i] = dp[i - 1];
            String num1 = s.substring(i - 2, i);
            if(num1.charAt(0) != '0' && Integer.valueOf(num1) < 26) dp[i] += dp[i - 2];
        }

        return dp[dp.length - 1];
    }
}
```



## 47. 礼物的最大价值

### 描述

[链接](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

```
在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

输入: 
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 12
解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物。
```

### 分析

动态规划，从右下角开始，记录每个点到右下角的最大收益即可

### 实现

```java
class Solution {
    public int maxValue(int[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        int row = grid.length - 1;
        int col = grid[0].length - 1;
        
        // 计算最后一行和最后一列
        for (int i = row - 1; i >= 0; i--) {
            grid[i][col] += grid[i + 1][col];
        }
        for (int j = col - 1; j >= 0; j--) {
            grid[row][j] += grid[row][j + 1];
        }

        for (int i = row - 1; i >= 0; i--) {
            for (int j = col - 1; j>= 0; j--) {
                grid[i][j] = Math.max(grid[i + 1][j], grid[i][j + 1]) + grid[i][j];
            }
        }
        return grid[0][0];
    }
}
```





## 48. 最长不含重复字符的字串

### 描述

[链接](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)

```
请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

### 分析

双指针，维护一个window， 里面是不重复的元素， 采用set保存。往右扩展，如果重复，删除直到不重复

### 实现

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {

        int left = 0, right = 0;
        Set<Character> window = new HashSet<>();
        int ans = 0;

        while (right < s.length()) {
            // 
            char c = s.charAt(right);
            while (window.contains(c)) {
                // 删除直到不重复
                window.remove(s.charAt(left));
                left += 1;       
            }
            window.add(c); // 加入
            ans = Math.max(ans, window.size());
            right += 1;
        }
        return ans;
    }
}
```



## 49. 丑数

### 描述

[链接](https://leetcode-cn.com/problems/chou-shu-lcof/)

```
我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。
输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
```

### 分析

分析了很多次了，看代码吧

### 实现

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] dp = new int[n + 1];
        dp[1] = 1;
        int index1 = 1, index2 = 1, index3 = 1;

        for (int i = 2; i <= n; i++) {
            int next = Math.min(dp[index1] * 2, Math.min(dp[index2] * 3, dp[index3] * 5));
            dp[i] = next;

            if (next == dp[index1] * 2) {
                index1 += 1;
            }

            if (next == dp[index2] * 3) {
                index2 += 1;
            }

            if (next == dp[index3] * 5) {
                index3 += 1;
            }
        }
        return dp[n];
    }
}
```



## 50. 第一个只出现一次的字符

### 题目描述

[链接](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

```
在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。
s = "abaccdeff"
返回 "b"

s = "" 
返回 " "
```

### 分析

由于只包含小写字母，使用一个26的数组来记录出现的此处，然后找第一个适合的即可

### 实现

```java
class Solution {
    public char firstUniqChar(String s) {
        int[] count = new int[26];
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a'] += 1;
        }
        for (int i = 0; i < s.length(); i++) {
            if (count[s.charAt(i) - 'a'] == 1) {
                return s.charAt(i);
            }
        }
        return ' ';
    }
}
```

