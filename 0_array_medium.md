---
title: 数组 Array (medium)
date: 2022-04-14 19:58:00
updated: 2022-04-14 21:41:00
tag:
- leetcode
- array
---

## 本章重点

1. /

## 本章题目思路记忆要点

1. **两数相加**：储存在倒序链表中的两个数字（个位在链表最外侧），求和时可以直接考虑边循环边直接相加<u>每一位</u>，保存进位数即可。

## 题目

### 1. 两数相加

> 给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。
>
> 请你将两个数相加，并以相同形式返回一个表示和的链表。
>
> 你可以假设除了数字 0 之外，这两个数都不会以 0 开头。
>
>  
>
> **示例 1**：
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220414201633996.png" alt="image-20220414201633996" width="50%" />
> </div>
>
> ```
> 输入：l1 = [2,4,3], l2 = [5,6,4]
> 输出：[7,0,8]
> 解释：342 + 465 = 807.
> ```
>
> **示例 2**：
>
> ```
> 输入：l1 = [0], l2 = [0]
> 输出：[0]
> ```
>
> **示例 3**：
>
> ```
> 输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
> 输出：[8,9,9,9,0,0,0,1]
> ```
>
> **提示**：
>
> - 每个链表中的节点数在范围` [1, 100] `内
> - `0 <= Node.val <= 9`
> - 题目数据保证列表表示的数字不含前导零

**原accept答案**：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        def read_listnode(node):
            num_list = []
            while node is not None:
                num_list.append(node.val)
                node = node.next
            num_list = num_list[::-1]
            num = 0
            for item in num_list:
                num = num * 10
                num += item
            return int(num)
        def construct_listnode(num):
            num_list = []
            if num == 0:
                num_list = [0]
            else:
                while num >= 1:
                    num_list.append(num % 10)
                    num = num // 10
            num_list = num_list[::-1]
            prev = None
            for idx, item in enumerate(num_list):
                tmp = ListNode(item, prev)
                prev = tmp
            return prev
        
        num1 = read_listnode(l1)
        num2 = read_listnode(l2)
        s = num1 + num2

        return construct_listnode(int(s))
```

**优化思路**：两个链表既然都是倒序的，自然可以直接通过<u>一次循环</u>来求和。注意进位的处理。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        add = 0 # 下次计算该加的数
        node = ListNode(-100)
        res = node # 要返回的指针
        while l1 is not None or l2 is not None:
            if l1 is not None and l2 is not None:
                result = l1.val + l2.val + add
                l1 = l1.next
                l2 = l2.next
            elif l1 is not None:
                result = l1.val + add
                l1 = l1.next
            else:
                result = l2.val + add
                l2 = l2.next
            # compute
            this = result % 10
            add = result // 10
            node.next = ListNode(this)
            node = node.next
        # 处理进位
        if add > 0:
            node.next = ListNode(add)
        return res.next
```

