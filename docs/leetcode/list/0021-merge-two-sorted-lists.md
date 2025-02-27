---
title: Leetcode 21.合并两个有序链表
description:   Leetcode 21.合并两个有序链表题解，双指针遍历解法
---

## 题目

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例:**

![](https://cdn.how2cs.cn/csguide/064227.png)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**Leetcode链接:**

[https://leetcode.cn/problems/merge-two-sorted-lists/description/](https://leetcode.cn/problems/merge-two-sorted-lists/description/)

## 解题思路

这道题思路很简单，由于 L1 和 L2 都是有序链表，将这两者合并，只需要用两个指针，同时遍历 L1和L2，根据两个链表节点的大小关系，逐个将小的添加到新链表尾部。

**解法如下:**


```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // 遍历，两个指针分别指向 l1和 l2，不断将小的数插向新的链表
        
        // 安排一个虚拟头结点，避免特殊处理头结点
        ListNode* dummy_head = new ListNode(0);
        
        // 需要记录一个指针记录前驱节点，
        ListNode* pre = dummy_head;
        // 当 l1 和 l2 都还未到末尾时遍历
        while(l1 != nullptr && l2 != nullptr) {
            // 找出二者中小的那一个，添加到 pre 末尾，同时更新对应的节点指针向后移动
            if (l1->val < l2->val) {
                pre->next = l1;
                l1 = l1->next;
            } else {
                pre->next = l2;
                l2 = l2->next;
            }
            pre = pre->next;
        }

        // 循环结束，可能是其中一个链表移动到了末尾，继续将剩下链表补充到新链表尾部
        // 这里就复用 l1 来表示剩下的那一个链表了，免得新定义一个变量
        if(l1 == nullptr) {
            l1 = l2;
        }
        // l1 不为空，则添加到链表尾部
        if(l1 != nullptr) {
            pre->next = l1;
        }
        // 真正的头结点是虚拟节点的下一个节点，当然这里有内存泄露（虚拟节点），刷题就不管了
        return dummy_head->next;
    }
};
```


**时间复杂度**: O(N/M): 因为只需要遍历一遍 L1 和 L2 中较短的那一个，即 N/M 中小的

**空间复杂度**: O(1)


