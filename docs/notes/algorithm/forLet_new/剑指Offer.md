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

