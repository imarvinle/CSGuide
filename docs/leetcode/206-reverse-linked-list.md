
# 206.反转链表

## 题目

给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。


**示例:**

>输入: 1->2->3->4->5->NULL
> 
>输出: 5->4->3->2->1->NULL


**Leetcode链接:**

[https://leetcode.cn/problems/reverse-linked-list/](https://leetcode.cn/problems/reverse-linked-list/)

## 解题思路

### 一、 新建节点头插法

这道题，最容易想到的做法就是从头到尾遍历一遍链表，然后使用**头插法**，不断将后面的元素插入到新链表的头部。

当然这种做法很显然浪费内存空间。

这里还是写一下：

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
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr) {
            return head;
        }
        // 新建一个头结点，初始化为 head 的值，next 置为 null
        ListNode* new_head = new ListNode(head->val, nullptr);

        // 从head下一个开始遍历
        head = head->next;
        // 遍历原来的链表
        while(head != nullptr) {
            // 新建一个节点，节点的 next 指向当前的新链表头节点
            ListNode *temp = new ListNode(head->val, new_head);
            head = head->next;
            // 新链表的头结点更新为最新的节点
            new_head = temp;
        }
        // 返回最新的头节点
        return new_head;
    }
};
```

毫无疑问，内存占用很高：

![](https://cdn.how2cs.cn/csguide/152832.png)

### 二. 原地头插法

当然，要想时间和空间效率都最优，显然不能新分配节点，只能在原来节点的基础上进行操作。

![](https://cdn.how2cs.cn/csguide/154023.png)

这里大家可以脑补过程，其实还是按照头插法的步骤，每一次取最新的节点，将该节点的 next 指针指向当前的最新头结点。

所以这里至少需要 2 个指针（即双指针），一个记录当前待处理的节点叫 **cur**，一个是当前的头结点，这里叫 **pre**。

那么整个交换的核心代码应该怎么写呢?

**第一步:**

我们得先把 cur 的 next 保存下来，因为按照头插法，会将 cur 的 next 指向 pre，那这样就丢掉了下一个待处理的节点，所以得先定义 next 暂存。

```ListNode* next = cur->next;```

**第二步:**

完成头插，也就是将 cur 的 next 指针，指向当前的头 pre

```cur->next = pre;```

**第三步：**

更新 cur 和 pre，不断向后移动，那么 cur 就应该更新为第一步中保存的 next。

同时 pre 更新为 cur (变为新的头了)

```c++
pre = cur;
cur = next;
```

这里要注意的是，需要先更新 pre，因为先更新 cur 的话，那么当前的 cur 就丢失了，就没法用来更新 pre 了。


**完整代码:**

我们再加上初始化的代码，最开始的时候，将 pre 初始化为链表的头节点，cur 就是第二个节点。

循环终止的条件为 cur 为 null，为 null 就说明走到了链表的尽头。

不过这里有个特别注意的点，对于 head 节点我们需要特殊处理，需要将 head 的 next 指针置空，因为反转完成后， head 将成为尾节点。


```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // 提前返回
        if (head == nullptr) {
            return nullptr;
        }
        // 将 pre 和 cur 分别初始化为第一个和第二个节点
        ListNode* pre = head;
        ListNode* cur = head->next;
        // 将 head 的 next 置空，因为它最后会成为尾节点，否则将会在 1、2两个节点之间形成环
        pre->next = nullptr;
        // 当前节点不为 null 则头插
        while(cur != nullptr) {
            // 暂存下一个待处理节点
            ListNode* next = cur->next;
            // 当前节点指向之前的头结点
            cur->next = pre;
            // 当前节点成为头结点
            pre = cur;
            // 下一个待处理节点成为当前节点
            cur = next;
        }
        // 返回新的头结点
        return pre;
    }
};
```

上面这种写法，也有人叫做双指针法。

这种方法，只需要遍历一遍列表，不消耗额外的内存。

**时间复杂度为 O(N)**

**空间复杂度为 O(1)**

上面这个写法看起来特殊处理了头结点，似乎不够简洁，可以简化为：

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // 提前返回
        if (head == nullptr) {
            return nullptr;
        }
        // 前一个节点为空
        ListNode* pre = nullptr;
        // 当前节点为头结点
        ListNode* cur = head;
    
        // 当前节点不为 null 则头插
        while(cur != nullptr) {
            // 暂存下一个待处理节点
            ListNode* next = cur->next;
            // 当前节点指向之前的头结点
            cur->next = pre;
            // 当前节点成为头结点
            pre = cur;
            // 下一个待处理节点成为当前节点
            cur = next;
        }
        // 返回新的头结点
        return pre;
    }
};
```

### 三. 递归法

为什么这里会想到用递归呢？

首先大部分能用循环遍历的，都可以改写为递归。

其次，这个反转链表可以拆解为相同的子问题解决。

比如 

1->2->3->4->5

可以拆解为:

链表 1 和 链表 2->3->4->5

但是在拆解为这两个部分的时候，需要将 2 的 next 指向1 （这一步其实是拆解完的子问题如何合并，我们的子问题统一拆解为长度为1 的链表，和长度为 N-1 的链表，那么长度为1 链表天然就已经反转好了，左边反转好的可以作为右边反转好的尾巴，而右边链表的尾巴恰好是相邻的这一个元素，即 2）


而 链表 2->3->4->5 可以拆解为

链表 2 和  链表 3->4->5

以此类推....

那么递归的终止条件就是，链表拆解到最小了。

其实判断条件和头插法类似:

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr) {
            return head;
        }
        return reverseListImp(nullptr, head);
    }
    
    ListNode* reverseListImp(ListNode* left, ListNode* right) {
        // 终止条件
        if (right == nullptr) {
            return left;
        }
        // 暂存下一次分割后的右边起始节点（右边下一个）
        ListNode* next = right->next;
        
        // 递归解决子问题，左边链表就一个节点 right，右边的链表起始节点为 next
        ListNode* new_head = reverseListImp(right, next);
        // 合并子问题，将右边的第一个节点的 next 指向左边
        right->next = left;
        return new_head;
    }
};
```

当然了，注意这里递归里的写法，因为 合并子问题也可以放到下一层递归之前做，不影响，所以也可以这样写，看起来更加的简洁:

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr) {
            return head;
        }
        return reverseListImp(nullptr, head);
    }
    
    ListNode* reverseListImp(ListNode* left, ListNode* right) {
        // 终止条件
        if (right == nullptr) {
            return left;
        }
        // 暂存下一次分割后的右边起始节点（右边下一个）
        ListNode* next = right->next;

        // 合并子问题，将右边的第一个节点的 next 指向左边
        right->next = left;
        // 递归解决子问题，左边链表就一个节点 right，右边的链表起始节点为 next
        return reverseListImp(right, next);
    }
};
