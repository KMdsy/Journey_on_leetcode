---
title: 数组 Array (medium)
date: 2022-06-05 14:00:00
updated: 2023-01-12 23:00:00
tag:
- leetcode
- array
---

## 本章重点

1. 

## 本章题目思路记忆要点

1. **快速排序**：选一个基准，使得左边的部分比该基准小，右边的部分比该基准大，然后分别对基准左边的子序列和基准右边的子序列再次快排。
1. 反转链表II：链表问题首先要加一个虚拟头节点

## 题目

### 1. 简化路径

> 给你一个字符串 `path` ，表示指向某一文件或目录的 Unix 风格 **绝对路径** （以 `'/'` 开头），请你将其转化为更加简洁的规范路径。
>
> 在 Unix 风格的文件系统中，一个点（`.`）表示当前目录本身；此外，两个点 （`..`） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂相对路径的组成部分。任意多个连续的斜杠（即，`'//'`）都被视为单个斜杠` '/'` 。 对于此问题，任何其他格式的点（例如，`'...'`）均被视为文件/目录名称。
>
> 请注意，返回的 **规范路径** 必须遵循下述格式：
>
> - 始终以斜杠` '/' `开头。
> - 两个目录名之间必须只有一个斜杠` '/'` 。
> - 最后一个目录名（如果存在）不能 以 '/' 结尾。
> - 此外，路径仅包含从根目录到目标文件或目录的路径上的目录（即，不含 `'.' `或` '..'`）。
>
> 返回简化后得到的 **规范路径** 。
>
> **示例 1**：
>
> ```
> 输入：path = "/home/"
> 输出："/home"
> 解释：注意，最后一个目录名后面没有斜杠。 
> ```
>
> **示例 2**：
>
> ```
> 输入：path = "/../"
> 输出："/"
> 解释：从根目录向上一级是不可行的，因为根目录是你可以到达的最高级。
> ```
>
> **示例 3**：
>
> ```
> 输入：path = "/home//foo/"
> 输出："/home/foo"
> 解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。
> ```
>
> **示例 4**：
>
> ```
> 输入：path = "/a/./b/../../c/"
> 输出："/c"
> ```
>
> **提示**：
>
> - `1 <= path.length <= 3000`
> -  ``path `由英文字母，数字，`'.'`，`'/' `或` '_' `组成。
> - `path `是一个有效的 Unix 风格绝对路径。

**解题思路**：本题只需要借助`str.split('/')`对路径进行分割，对于分割后的结果（共有三种情况）分别作出判断即可。

**原accept答案**：

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        dir_name = path.split('/')
        # 分割后的字符串中包含
        # 1. 文件名
        # 2. . or ..
        # 3. 空
        output = []
        for name in dir_name:
            if name == '' or name == '.':
                pass
            elif name == '..':
                if len(output) > 0:
                    output.pop()
            else:
                output.append(name)
        return '/' + '/'.join(output)
```

### 1807. 替换字符串中的括号内容

> 给你一个字符串 `s` ，它包含一些括号对，每个括号中包含一个 **非空** 的键。
>
> - 比方说，字符串 `"(name)is(age)yearsold"` 中，有 **两个** 括号对，分别包含键 `"name"` 和 `"age"` 。
>
> 你知道许多键对应的值，这些关系由二维字符串数组 `knowledge` 表示，其中 `knowledge[i] = [keyi, valuei]` ，表示键 `keyi` 对应的值为 `valuei` 。
>
> 你需要替换 **所有** 的括号对。当你替换一个括号对，且它包含的键为 `keyi` 时，你需要：
>
> - 将 `keyi` 和括号用对应的值 `valuei` 替换。
> - 如果从 `knowledge` 中无法得知某个键对应的值，你需要将 `keyi` 和括号用问号 `"?"` 替换（不需要引号）。
>
> `knowledge` 中每个键最多只会出现一次。`s` 中不会有嵌套的括号。
>
> 请你返回替换 **所有** 括号对后的结果字符串。
>
>  
>
> **示例 1：**
>
> ```
> 输入：s = "(name)is(age)yearsold", knowledge = [["name","bob"],["age","two"]]
> 输出："bobistwoyearsold"
> 解释：
> 键 "name" 对应的值为 "bob" ，所以将 "(name)" 替换为 "bob" 。
> 键 "age" 对应的值为 "two" ，所以将 "(age)" 替换为 "two" 。
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "hi(name)", knowledge = [["a","b"]]
> 输出："hi?"
> 解释：由于不知道键 "name" 对应的值，所以用 "?" 替换 "(name)" 。
> ```
>
> **示例 3：**
>
> ```
> 输入：s = "(a)(a)(a)aaa", knowledge = [["a","yes"]]
> 输出："yesyesyesaaa"
> 解释：相同的键在 s 中可能会出现多次。
> 键 "a" 对应的值为 "yes" ，所以将所有的 "(a)" 替换为 "yes" 。
> 注意，不在括号里的 "a" 不需要被替换。
> ```
>
>  
>
> **提示：**
>
> - `1 <= s.length <= 105`
> - `0 <= knowledge.length <= 105`
> - `knowledge[i].length == 2`
> - `1 <= keyi.length, valuei.length <= 10`
> - `s` 只包含小写英文字母和圆括号 `'('` 和 `')'` 。
> - `s` 中每一个左圆括号 `'('` 都有对应的右圆括号 `')'` 。
> - `s` 中每对括号内的键都不会为空。
> - `s` 中不会有嵌套括号对。
> - `keyi` 和 `valuei` 只包含小写英文字母。
> - `knowledge` 中的 `keyi` 不会重复。

```python
class Solution:
    def evaluate(self, s: str, knowledge: List[List[str]]) -> str:
        '''
        思路：遍历字符串，一边识别一边替换
        '''
        n = len(s)
        # 替换已有的知识
        knowledge_dict = {}
        for item in knowledge:
            knowledge_dict[item[0]] = item[1]

        name = []
        s = list(s)
        start = False
        for i in range(n):
            if s[i] == '(':
                start = True
                pos1 = i
                continue
            if start is True:
                if s[i] == ')':
                    start = False
                    pos2 = i
                    # 替换
                    for idx in range(pos1, pos2+1):
                        if idx == pos1:
                            s[idx] = knowledge_dict.get(''.join(name), '?')
                        else:
                            s[idx] = ''
                    name = []
                    continue
                name.append(s[i])
        return ''.join(s)
```

### 1487. 保证文件名唯一

> 给你一个长度为 `n` 的字符串数组 `names` 。你将会在文件系统中创建 `n` 个文件夹：在第 `i` 分钟，新建名为 `names[i]` 的文件夹。
>
> 由于两个文件 **不能** 共享相同的文件名，因此如果新建文件夹使用的文件名已经被占用，系统会以 `(k)` 的形式为新文件夹的文件名添加后缀，其中 `k` 是能保证文件名唯一的 **最小正整数** 。
>
> 返回长度为 *`n`* 的字符串数组，其中 `ans[i]` 是创建第 `i` 个文件夹时系统分配给该文件夹的实际名称。
>
>  
>
> **示例 1：**
>
> ```
> 输入：names = ["pes","fifa","gta","pes(2019)"]
> 输出：["pes","fifa","gta","pes(2019)"]
> 解释：文件系统将会这样创建文件名：
> "pes" --> 之前未分配，仍为 "pes"
> "fifa" --> 之前未分配，仍为 "fifa"
> "gta" --> 之前未分配，仍为 "gta"
> "pes(2019)" --> 之前未分配，仍为 "pes(2019)"
> ```
>
> **示例 2：**
>
> ```
> 输入：names = ["gta","gta(1)","gta","avalon"]
> 输出：["gta","gta(1)","gta(2)","avalon"]
> 解释：文件系统将会这样创建文件名：
> "gta" --> 之前未分配，仍为 "gta"
> "gta(1)" --> 之前未分配，仍为 "gta(1)"
> "gta" --> 文件名被占用，系统为该名称添加后缀 (k)，由于 "gta(1)" 也被占用，所以 k = 2 。实际创建的文件名为 "gta(2)" 。
> "avalon" --> 之前未分配，仍为 "avalon"
> ```
>
> **示例 3：**
>
> ```
> 输入：names = ["onepiece","onepiece(1)","onepiece(2)","onepiece(3)","onepiece"]
> 输出：["onepiece","onepiece(1)","onepiece(2)","onepiece(3)","onepiece(4)"]
> 解释：当创建最后一个文件夹时，最小的正有效 k 为 4 ，文件名变为 "onepiece(4)"。
> ```
>
> **示例 4：**
>
> ```
> 输入：names = ["wano","wano","wano","wano"]
> 输出：["wano","wano(1)","wano(2)","wano(3)"]
> 解释：每次创建文件夹 "wano" 时，只需增加后缀中 k 的值即可。
> ```
>
> **示例 5：**
>
> ```
> 输入：names = ["kaido","kaido(1)","kaido","kaido(1)"]
> 输出：["kaido","kaido(1)","kaido(2)","kaido(1)(1)"]
> 解释：注意，如果含后缀文件名被占用，那么系统也会按规则在名称后添加新的后缀 (k) 。
> ```
>
>  
>
> **提示：**
>
> - `1 <= names.length <= 5 * 10^4`
> - `1 <= names[i].length <= 20`
> - `names[i]` 由小写英文字母、数字和/或圆括号组成。

```python
class Solution:
    def getFolderNames(self, names: List[str]) -> List[str]:
        '''
        复杂度不能太高
        对于一个名字，是否能够命名，需要
        1. 原名称是否被占用 (set)
        2. 原来名称被占用了几次
        3. 更改后的名称是否被占用
        更改后的名称永远遵循 原名称(k) 的规则，被占用了几次，k就从0加几次
        '''
        used = set()
        res = []
        for name in names:
            if name not in used:
                res.append(name)
                used.add(name)
            else:
                # 开始计数
                k = 1
                while name+f'({k})' in used:
                    k += 1
                res.append(name+f'({k})')
                used.add(name+f'({k})')
        return res
```

### 面试题 05.02. 二进制数转字符串

> 二进制数转字符串。给定一个介于0和1之间的实数（如0.72），类型为double，打印它的二进制表达式。如果该数字无法精确地用32位以内的二进制表示，则打印“ERROR”。
>
> **示例1:**
>
> ```
>  输入：0.625
>  输出："0.101"
> ```
>
> **示例2:**
>
> ```
>  输入：0.1
>  输出："ERROR"
>  提示：0.1无法被二进制准确表示
> ```
>
>  
>
> **提示：**
>
> - 32位包括输出中的 `"0."` 这两位。
> - 题目保证输入用例的小数位数最多只有 `6` 位

```python
class Solution:
    def printBin(self, num: float) -> str:
        '''
        感觉无需考虑复杂度，用二进制的定义即可，用30位数字表示
        '''
        res = '0.'
        i = 1
        while num > 0:
            if len(res) >= 32:
                return 'ERROR'
            n = int(num // 2**(-i))
            if n == 0:
                res += str(n)
            else:
                res += str(n)
                num -= 2**(-i)
            i += 1
        return res
```

### 215. 数组中的第K个最大元素 <a name="K_largest">📌</a>

> 给定整数数组 `nums` 和整数 `k`，请返回数组中第 `**k**` 个最大的元素。
>
> 请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。
>
> 你必须设计并实现时间复杂度为 `O(n)` 的算法解决此问题。
>
> **示例 1:**
>
> ```
> 输入: [3,2,1,5,6,4], k = 2
> 输出: 5
> ```
>
> **示例 2:**
>
> ```
> 输入: [3,2,3,1,2,4,5,5,6], k = 4
> 输出: 4
> ```
>
> **提示：**
>
> - `1 <= k <= nums.length <= 105`
> - `-104 <= nums[i] <= 104`

```python
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        '''
        快排思路：选一个基准，使得左边的部分比该基准大，右边的部分比该基准小（构成倒序的序列）
        当选择的基准是第k-1个索引的时候，返回该值（即第k大的值）
        本题的主要思路是，借助“随机选择基准”，避免恶意的测试样例（将最小的数放在固定的位置）
        此外，算法只要找到第k大的数字即可，无需全部排序，因此可以降低（平均）时间复杂度到O(n)
        '''
        # 快排实现
        def sort(nums, L, R, K):
            base_idx = random.randint(L, R)
            target = nums[base_idx]
            
            l, r = L, R
            nums[base_idx] = nums[l] # 第一次交换的时候，l位置的值会丢失，保存一下
            while l < r:
                while l < r and nums[r] < target: # 找到第一个右侧小于target的数字
                    r -= 1
                if l < r: # 找到了
                    nums[l] = nums[r]
                    l += 1
                while l < r and nums[l] > target: # 找到第一个左侧大于target的数字
                    l += 1
                if l < r:
                    nums[r] = nums[l]
                    r -= 1
            # r == l
            nums[r] = target
            
            if r == K: 
                return nums[r]
            elif r > K: # 在大的部分找
                return sort(nums, L, r-1, K)
            else:
                return sort(nums, r+1, R, K)

        return sort(nums, 0, len(nums)-1, k-1)
```

### 92. 反转链表 II

> 给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。
>
>  
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)
>
> ```
> 输入：head = [1,2,3,4,5], left = 2, right = 4
> 输出：[1,4,3,2,5]
> ```
>
> **示例 2：**
>
> ```
> 输入：head = [5], left = 1, right = 1
> 输出：[5]
> ```
>
>  
>
> **提示：**
>
> - 链表中节点数目为 `n`
> - `1 <= n <= 500`
> - `-500 <= Node.val <= 500`
> - `1 <= left <= right <= n`

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        if head.next is None:
            return head
        if left == right:
            return head
        # 思路：链表不长，可以直接拆分成三段，反转部分列表
        
        xx = ListNode(next=head) # 虚拟头节点
        pin = xx
        cnt = 0 # pin的位置
        
        while pin is not None:
            if cnt + 1 == left:
                store1 = pin
                new_head = pin.next
            if cnt == right:
                store2 = pin.next
                pin.next = None
                break
            pin = pin.next
            cnt += 1
        store1.next = None
        # 至此，拆分为了三部分
        prev, ne = new_head, new_head.next
        new_head.next = None
        while ne is not None:
            tmp = ne.next
            ne.next = prev
            prev = ne
            ne = tmp
        store1.next = prev
        new_head.next = store2
        return xx.next
```

### 82. 删除排序链表中的重复元素 II

> 给定一个已排序的链表的头 `head` ， *删除原始链表中所有重复数字的节点，只留下不同的数字* 。返回 *已排序的链表* 。
>
>  
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2021/01/04/linkedlist1.jpg)
>
> ```
> 输入：head = [1,2,3,3,4,4,5]
> 输出：[1,2,5]
> ```
>
> **示例 2：**
>
> ![img](https://assets.leetcode.com/uploads/2021/01/04/linkedlist2.jpg)
>
> ```
> 输入：head = [1,1,1,2,3]
> 输出：[2,3]
> ```
>
>  
>
> **提示：**
>
> - 链表中节点数目在范围 `[0, 300]` 内
> - `-100 <= Node.val <= 100`
> - 题目数据保证链表已经按升序 **排列**

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        new_head = ListNode(next=head)
        # 节点数不多，维护一个列表记录节点的个数及位置
        pos = {} # key: val, value: [pos_pin-1][pos_pin]
        prev, ne = new_head, new_head.next
        delete_set = []
        while ne is not None:
            if ne.val not in pos.keys():
                pos[ne.val] = []
            pos[ne.val].append(ne) # 找到位置
            if len(pos[ne.val]) > 1:
                delete_set.append(ne.val) # 要删除哪些
            ne = ne.next
        # 删除delete_set中的节点
        all_num = list(pos.keys())
        all_num.sort()
        delete_set = list(set(delete_set))
        for del_num in delete_set:
            # all_num中找到前缀的节点
            prev_idx = all_num.index(del_num) - 1
            if prev_idx < 0:
                prev_pin = new_head
            else:
                prev_num = all_num[prev_idx]
                prev_pin = pos[prev_num][0]
            # 找到后缀节点
            next_idx = all_num.index(del_num) + 1
            if next_idx >= len(all_num):
                next_pin = None
            else:
                next_num = all_num[next_idx]
                next_pin = pos[next_num][0]
            # 删除中间的部分
            prev_pin.next = next_pin
            # 更新集合all_num
            all_num.remove(del_num)
        return new_head.next
```

### 142. 环形链表 II

> 给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*
>
> 如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。
>
> **不允许修改** 链表。
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)
>
> ```
> 输入：head = [3,2,0,-4], pos = 1
> 输出：返回索引为 1 的链表节点
> 解释：链表中有一个环，其尾部连接到第二个节点。
> ```
>
> **示例 2：**
>
> ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)
>
> ```
> 输入：head = [1,2], pos = 0
> 输出：返回索引为 0 的链表节点
> 解释：链表中有一个环，其尾部连接到第一个节点。
> ```
>
> **示例 3：**
>
> ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)
>
> ```
> 输入：head = [1], pos = -1
> 输出：返回 null
> 解释：链表中没有环。
> ```
>
>  
>
> **提示：**
>
> - 链表中节点的数目范围在范围 `[0, 104]` 内
> - `-105 <= Node.val <= 105`
> - `pos` 的值为 `-1` 或者链表中的一个有效索引

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        new_head = ListNode(-1)
        new_head.next = head
        # 设置一个set存储遍历过的链表
        pin = new_head
        sset = set()
        while pin is not None:
            # 先评估这个节点是否指向一个已有的节点
            if pin.next in sset:
                return pin.next
            else:
                # 如果没有，这个节点可以遍历
                sset.add(pin)
            pin = pin.next
        return None
```

### 739. 每日温度

> 给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。
>
> **示例 1:**
>
> ```
> 输入: temperatures = [73,74,75,71,69,72,76,73]
> 输出: [1,1,4,2,1,1,0,0]
> ```
>
> **示例 2:**
>
> ```
> 输入: temperatures = [30,40,50,60]
> 输出: [1,1,1,0]
> ```
>
> **示例 3:**
>
> ```
> 输入: temperatures = [30,60,90]
> 输出: [1,1,0]
> ```
>
> **提示：**
>
> - `1 <= temperatures.length <= 10^5`
> - `30 <= temperatures[i] <= 100`

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        import numpy as np
        '''
        倒序问题：在第i天前，哪天的温度比今天高
        '''
        temperatures_re = temperatures[::-1]
        # 一个字典，保存i位置之前所有温度的所在的位置
        # 每次查询的时候
        # 1. 查找是否有比当前位置更高的温度
        # 2. 输出距离i最近、具有更高温度的一天
        # 3. 结果即为两天索引差值
        record = {} # key: temp, value: day
        res = []
        for i, temp in enumerate(temperatures_re):
            if i == 0:
                res.append(0)
                record[temp] = []
                record[temp].append(i)
            else:
                if max(record.keys()) > temp:
                    # 历史上有比今天更高的温度，遍历所有具有更高温度的一天
                    latest_day = -1
                    for k, v in record.items():
                        if k > temp:
                            latest_day = max([latest_day, v[-1]]) # 取最近的一天
                    res.append(i - latest_day)
                else:
                    res.append(0)
                # 当天记录
                if record.get(temp) is None:
                    record[temp] = []
                record[temp].append(i)
        return res[::-1]
```
