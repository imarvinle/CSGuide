---
title: Leetcode 203.移除链表元素
description:  Leetcode第203题，移除链表元素解题思路和题解，双指针法以及虚拟头结点
meta:
  - name: description
    content:   Leetcode第203题，移除链表元素解题思路和题解，双指针法以及虚拟头结点
  - name: keywords
    content: 203.移除链表元素、Leetcode、剑指offer
---

# 203.移除链表元素

## 题目

给你一个链表的头节点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的节点，并返回 **新的头节点** 。

**示例:**

![](https://cdn.how2cs.cn/csguide/144851.png)

>```
>输入：head = [1,2,6,3,4,5,6], val = 6
>输出：[1,2,3,4,5]
>```

**Leetcode链接:**

[https://leetcode.cn/problems/remove-linked-list-elements/description/](https://leetcode.cn/problems/remove-linked-list-elements/description/)

## 解题思路

这道题非常显而易见，从单向链表中移除元素，只需要从头到尾遍历链表，如果找到需要移除的节点，那么直接将该节点从链表剔除即可。
如何剔除节点 B？
那就是将节点 B 的前一个节点和下一个节点直接相连。
假设，节点 B 的前一个节点是 A：
那么就是 
A->next = B->next;

所以问题的关键就是，在用指针 cur 遍历链表的时候，得有一个额外的指针 pre 来记录前驱节点。

但是有一个问题，是链表问题中常见的，就是删除头结点和中间节点的操作不一样。

**删除中间节点:**

pre->next = cur->next;
delete cur;

**删除头节点:**

删除头结点，主要是需要更新最新的头结点为下一个节点

// 暂存

ListNode* next = cur->next;

delete cur;

cur = next;

// 更新头节点

head = next;



上面是分别表示遇到头结点和中间节点应该如何删除的问题。



实际问题中我们遇到的大多是以下几类：



1. 头结点需要删除（可能头结点需要连续删除）

1->2->3, val = 1;

2. 中间节点需要删除（可能连续删除）

1->2->2->5, val = 2

3. 头结点和中间节点都需要删除

1->2->3->1->5, val = 1

## 写法一


**完整代码:**

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        // 双指针，需要一个额外的指针保存前驱节点
        ListNode *pre = nullptr;
        ListNode *cur = head;
        while(cur != nullptr) {
            // 如果等于 val
            if (cur->val == val) {
                // 删除节点cur，即将前一个节点的 next 指向当前节点的下一个节点
                // 前一个节点非空
                if(pre != nullptr) {
                  pre->next = cur->next;
                } else {
                  // 前一个为空，则说明当前节点是现存的第一个节点，同时呢，当前节点又会被删除，所以新链表的头结点，一定是下一个节点（）
                  // 如果遇到 1->1->1->1->2  val = 1，则代码会多次走到这里
                  head = cur->next;
                }
                
                ListNode* temp = cur;
                cur = cur->next;
                delete temp;
            }  else {
                // 不等于 val，则 pre 和 cur 都分别向后移动
                pre = cur;
                cur = cur->next;
            }
        }
        return head;
    }
};
```



## 写法二

当然啦，上面这种写法是将头结点和中间节点的识别放在了一个循环中，我们也可以将头结点的处理和中间节点的处理分开。


第一步：

先循环遍历，如果需要删除头结点，则不断移除，直到头结点不需要移除

第二步：

继续循环遍历，如果有需要移除的中间节点，移除之~


**完整代码:**

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        // 第一步：移除头结点（头结点可能连续需要移除）
        while(head != nullptr && head->val == val) {
            ListNode* next = head->next;
            delete head;
            head = next;
        }

        // 如果head 为空提前返回，这里提前返回，其实是为了后续统一处理，避免到处去判断 head 是否为空
        if(head == nullptr) {
            return head;
        }

        // 第二步: 移除中间元素
        
        // 移除中间元素需要一个临时变量记录前驱节点
        ListNode* pre = head;      // 因为走到这里说明head一定不为空，且 head.val != val
        ListNode* cur = head->next;  // 这样赋值一定要确保 head 不为空
        while(cur != nullptr) {
            if (cur->val == val) {
                pre->next = cur->next;
                ListNode* temp = cur->next;
                delete cur;
                cur = temp; // 其实这里也可以不新定义temp，而使用 pre->next 这二者都是等价的，不过这样写更清晰
            } else {
                // 不等，则 pre 和 cur 都向后移动
                pre = cur;
                cur = cur->next;
            }
        }
        return head;
    }
    
};
```

## 写法三：虚拟头结点

上面我们看到了，无论是哪种写法，都需要特殊判断是不是头结点。

那如果我们能在当前链表的前面加一个虚拟的头结点，那么我们真正要操作的链表节点就全部成了中间节点了。

这样操作就统一了。

虚拟头结点也是在链表各种操作中用的比较多的，一般就是头结点不存放实际数据，即为虚拟头结点。


**完整代码:**
```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        // 新增一个虚拟头节点，执行 head
        ListNode *dummy_head = new ListNode(0, head);

        ListNode *pre = dummy_head;
        // 这是真正需要处理的节点开始
        ListNode *cur = dummy_head->next;
        while(cur != nullptr) {
            if(cur->val == val) {
                pre->next = cur->next;
                delete cur;
                cur = pre->next;  // 这里就直接使用 pre->next 而不再单独定义一个临时变量
            } else {
                pre = cur;
                cur = cur->next;
            }
        }
        // 需要返回真正的节点开始，即 dummy_head 的下一个
        return dummy_head->next;
    }
    
};
```

这样代码的操作看起来简洁很多了~




