# 剑指 offer



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

