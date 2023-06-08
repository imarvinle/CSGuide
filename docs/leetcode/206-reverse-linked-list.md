
# 206.反转链表

## 题目：

给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。


### 示例

![](https://cdn.how2cs.cn/csguide/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_fa06f681-5748-4b22-b944-7d7f4077c829.png)


输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]


## 解题思路

### 1. 新建节点头插法

首先这道题，最简单的做法就是从头到尾遍历一遍链表，然后使用**头插法**，不断将后面的元素插入到链表的头部。

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

### 2. 原地头插法

当然，要想时间和空间效率都最优，显然不能新分配节点，只能在原来节点的基础上进行操作。


![](https://cdn.how2cs.cn/csguide/154023.png)

这里大家可以脑补过程，其实还是按照头插法的方法，每一次取最新的节点，将该节点的 next 指针指向当前的最新头结点。

所以这里至少需要 2 个指针，一个记录当前待处理的节点叫 cur，一个是当前的头结点，这里叫 pre;

那么整个核心代码应该怎么写呢?

第一步：

我们得先把 cur 的 next 保存下来，因为待会会将 cur 的next 指向 pre，完成头插。

ListNode* next = cur->next;

第二步:

完成头插，也就是将 cur 的 next 指针，指向当前的头，也就是 pre

cur->next = pre;

第三步：

更新 cur 和 pre，不断向后移动，那么 cur 就应该更新为我们保存的 next。

同时 pre 更新为 cur (变为新的头了)

pre = cur;

cur = next;

这里要注意的是，需要先更新 pre，因为先更新 cur 的话，那么当前的 cur 就丢失了。

**完整代码:**

我们再加上初始化的代码，最开始将 pre 初始化为链表的头节点，cur 就是第二个节点。

循环终止的条件为 cur 为 null，为 null 就说明走到了链表的尽头。

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr) {
            return nullptr;
        }
        ListNode* pre = head;
        ListNode* cur = head->next;
        pre->next = nullptr;
        while(cur != nullptr) {
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
};
```

上面这种写法，也有人叫做双指针法。

这种方法，只需要遍历一遍列表，不消耗额外的内存。

**时间复杂度为 O(N)**

**空间复杂度为 O(1)**

