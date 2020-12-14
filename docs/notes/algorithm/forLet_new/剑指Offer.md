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

