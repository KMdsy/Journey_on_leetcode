---
title: 数组 Array (medium)
date: 2022-04-14 19:58:00
updated: 2022-04-17 21:38:00
tag:
- leetcode
- array
---

## 本章重点

1. 滑动窗口法：双指针+haspmap（<a href="#longest_substr_no_repeat">查找最长不重复子串问题</a>）
2. 动态规划法，详见<a href="#longest_palindromic_substr">最长回文子串问题</a>

## 本章题目思路记忆要点

1. **两数相加**：储存在倒序链表中的两个数字（个位在链表最外侧），求和时可以直接考虑边循环边直接相加<u>每一位</u>，保存进位数即可。
2. **无重复的最长子串**：基于双指针的滑动窗口法，涉及<u>“无重复”</u>均直接采用`set`或者`dict`实现的`hashmap`。

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

## 2. 无重复字符的最长子串<a id="longest_substr_no_repeat"> 📌</a>

> 给定一个字符串` s `，请你找出其中不含有重复字符的 **最长子串** 的长度。
>
> **示例 1**:
>
> ```
> 输入: s = "abcabcbb"
> 输出: 3 
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
> ```
>
> **示例 2**:
>
> ```
> 输入: s = "bbbbb"
> 输出: 1
> 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
> ```
>
> **示例 3**:
>
> ```
> 输入: s = "pwwkew"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
>      请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
> ```
>
> **提示**：
>
> - `0 <= s.length <= 5 * 104`
> - `s `由英文字母、数字、符号和空格组成

**原accept答案**：

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int: 
        str_len = len(s)
        longest = 0
        for start_idx in range(str_len):
            hashmap = set() # 始终存储一个不重复的子串
            end_idx = start_idx
            while end_idx < str_len and s[end_idx] not in hashmap:
                hashmap.add(s[end_idx])
                end_idx += 1
            longest = max([longest, len(hashmap)])
        return longest
```

**解题思路**：本题采用<u>滑动窗口法</u>：

- <u>左指针</u>：依次指向每个字符
- <u>右指针</u>：从左指针开始滑动到字符串尾
- 判断左右指针包围的区域是否符合要求
- 涉及到<u>不重复</u>概念的，一律使用`set`或者`dict`解决

**反直觉的**：以下方法是超出时间限制的——枚举长度为`len(s)`到`1`的窗口，并让窗口左右滑动，判断窗口内的子串是否符合要求。

**优化思路**：上述accept答案耗时和内容消耗过多（执行用时：`488 ms`, 在所有 Python3 提交中击败了`9.58%`的用户；内存消耗：`15.2 MB,` 在所有 Python3 提交中击败了`19.79%`的用户）。考虑不在每次循环的时候新建`set()`。

**优化答案**：执行用时：`88 ms`, 在所有 Python3 提交中击败了`29.87%`的用户；内存消耗：`15.1 MB`, 在所有 Python3 提交中击败了`38.40%`的用户。

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int: 
        hashmap = set() # 始终存储一个不重复的子串
        str_len = len(s)
        longest = 0
        end_idx = -1
        for start_idx in range(str_len):
            if start_idx != 0:
                hashmap.remove(s[start_idx - 1])
            while end_idx + 1 < str_len and s[end_idx + 1] not in hashmap:
                # end_idx 先验证，再赋值
                # 这步操作保证了，end_idx一直指向一个不重复子串的末尾
                hashmap.add(s[end_idx + 1])
                end_idx += 1
            longest = max([longest, len(hashmap)])
        return longest
```

## 3. 最长回文子串 <a id="longest_palindromic_substr">📌</a>

> 给你一个字符串` s`，找到` s `中最长的回文子串。 
>
> **示例 1**：
>
> ```
> 输入：s = "babad"
> 输出："bab"
> 解释："aba" 同样是符合题意的答案。
> ```
>
> **示例 2**：
>
> ```
> 输入：s = "cbbd"
> 输出："bb"
> ```
>
> **提示**：
>
> - `1 <= s.length <= 1000`
> - `s `仅由数字和英文字母组成

**动态规划算法**<a id="dynamic_programing">🌟</a>

动态规划的答题思想 [link](https://zhuanlan.zhihu.com/p/365698607)，目前看懂了写不出来😭





**Backup超时代码**：思路没问题，按照滑动窗口的思想写了暴力破解，超时了😭

```python
class Solution:
    def is_palindrome(self, s: str):
        if len(s) == 0:
            return False, ''
        elif len(s) == 1:
            return True, s
        med = len(s) // 2
        if s[0:med][::-1] == s[-med:]:
            return True, s
        return False, ''

    def longestPalindrome(self, s: str) -> str:
        str_len = len(s)
        longest = ''
        for start_idx in range(str_len):
            end_idx = start_idx - 1
            # 检查start_idx到end_idx是不是回文串
            while end_idx + 1 <= str_len:
                res, res_str = self.is_palindrome(s[start_idx : end_idx+1])
                if res is True and len(res_str) > len(longest):
                    longest = res_str
                end_idx += 1
        return longest
```

