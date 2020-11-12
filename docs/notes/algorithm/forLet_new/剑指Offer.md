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

