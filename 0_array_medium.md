---
title: 数组 Array (medium)
date: 2022-04-14 19:58:00
updated: 2022-12-26 23:48:00
tag:
- leetcode
- array
---

## 本章重点

1. 滑动窗口法（碰到需要双重遍历的题目，应当首先想到该方法）：
    - 双指针+hashmap（[查找最长不重复子串问题](#longest_substr_no_repeat)）、
    - 双指针（[盛最多水的问题](#largest_area)）、
    - 双指针+排序（[三数之和问题](#three_sum)、[最接近的三数之和](#closest_target)、[四数之和问题](#four_sum)、[搜索旋转排序问题](#rotation_search)、[在排序数组中查找元素的第一个和最后一个位置](#search_first_and_last)、[合并区间](#combine_intervals)）
2. 动态规划法，详见<a href="#longest_palindromic_substr">最长回文子串问题</a>
3. 回溯（backtrack）：[电话号码的所有组合](#comb_phonenumber)、[组合总和](#comb_sum)、[组合总和II](#comb_sum2)、[单词搜索](#word_search)
4. 二分法 + 快速乘法：[两数相除](#two_divide)
5. [下一个排列](#next_comb): 排列的重要题目
6. [全局倒置与局部倒置](#two_reverse): 数学解法很刁钻，从数组生成的角度看待数据
7. [最长连续数列](#longest_con_array): 区间合并的思路，这个多记忆一下
8. [只出现一次的数字](#number_once): 异或运算满足交换律：`a^b^a = a^a^b = b`
9. [统计同构子字符串的数目](#count_same_str): `groupby`函数的用法

## 本章题目思路记忆要点

1. **两数相加**：储存在倒序链表中的两个数字（个位在链表最外侧），求和时可以直接考虑边循环边直接相加<u>每一位</u>，保存进位数即可。
2. **无重复的最长子串**：基于<u>双指针</u>的滑动窗口法，涉及<u>“无重复”</u>均直接采用`set`或者`dict`实现的`hashmap`。
3. **Z字形变换**：注意答案要求，仅考虑每一行存储的字母顺序即可，无需精确到具体的坐标
4. **整数转罗马字**：数字转罗马字的思路为，对数字`num`寻找不超过该数的符号表示，然后`num`减去可被符号表示的部分，直到`num`为0
5. **整数翻转**：假设环境中不允许存储64位数字，那我们需要在乘10运算之前，将output与`2^31 // 10`做比较以保证不超过该数。
6. **删除链表的倒数第N个节点**：可以用双指针法，注意哑节点（指向头部节点的虚拟节点）的使用，可以避免头节点的判断。
7. **生成括号**：生成包含`n`个括号的问题`gp(n)`，其基本形式为`(a)b`，`a`, `b`分别代表一个有效的括号序列，`a`, `b`可以为空。对偶问题——[验证括号有效性](./0_array_easy.md#valid_parentheses)
8. 

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

## 4. Z字形变换

> 将一个给定字符串` s `根据给定的行数` numRows `，以从上往下、从左到右进行 Z 字形排列。
>
> 比如输入字符串为 "PAYPALISHIRING" 行数为 3 时，排列如下：
>
> ```
> P   A   H   N
> A P L S I I G
> Y   I   R
> ```
>
> 之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"PAHNAPLSIIGYIR"`。
>
> 请你实现这个将字符串进行指定行数变换的函数：
>
> ```
> string convert(string s, int numRows);`
> ```
>
> **示例 1**：
>
> ```
> 输入：s = "PAYPALISHIRING", numRows = 3
> 输出："PAHNAPLSIIGYIR"
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "PAYPALISHIRING", numRows = 4
> 输出："PINALSIGYAHRPI"
> 解释：
> P     I    N
> A   L S  I G
> Y A   H R
> P     I
> ```
>
> **示例 3：**
>
> ```
> 输入：s = "A", numRows = 1
> 输出："A"
> ```
>
> 
>
> **提示：**
>
> - `1 <= s.length <= 1000`
> - `s `由英文字母（小写和大写）、`',' `和` '.' `组成
> - `1 <= numRows <= 1000`

**解题思路**：在首次解题的时候，通过分别研究每个Z字排列的字母坐标的横纵坐标值，发现如下规律，设`numRows = 4`：

- 横坐标变换规律：`[0, 1, 2, 3, 2, 1, 0, 1, 2, 3, ...]`
- 纵坐标变换规律：`[0, 0, 0, 0, 1, 2, 3, 3, 3, 3, 4, 5, ...]`，该序列可以视为`[0, 0, 0, 0, 1, 2] + 3*[0, 0, 0, 0, 1, 2] + 6*[0, 0, 0, 0, 1, 2] + ...`

原accept代码即根据上述规律编写而成。

**优化思路**：从答案出发，其实我们只关注每一行中字符的相对位置，而不关注他们具体的坐标，因此只需要用二维列表进行字母的存储即可。

**优化代码**：

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        # [0, 1, 2, 3, 2, 1]
        row_list = [item for item in range(numRows)] + [item for item in range(numRows-2, 0, -1)]
        row_pin = 0
        store_list = [[] for _ in range(numRows)]
        for _, letter in enumerate(s):
            row_idx = row_list[row_pin]
            store_list[row_idx].append(letter)
            if row_pin + 1 == len(row_list):
                row_pin = 0
            else:
                row_pin += 1
        # output
        output = []
        for row in store_list:
            for item in row:
                output.append(item)
        return ''.join(output)
```

**原accept代码**：

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        read_dict = {} # key: row_idx, value: list of col_idx
        letter_dict = {} # key: (row_idx, col_idx), value: letter
        row_idx_list = [item for item in range(numRows)] # [0, 1, 2, 3]
        col_idx_list = [0] * numRows + [item for item in range(1, numRows-1)] # [0, 0, 0, 0, 1, 2]
        # 指向本次的字母坐标位置
        col_pin = 0
        col_base = numRows - 1 # 每次更新列表的时候要加的数
        row_pin = 0
        row_dir = 'up' # row_pin的移动方向
        for letter in s:
            if read_dict.get(row_idx_list[row_pin]) is None:
                read_dict[row_idx_list[row_pin]] = []
            read_dict[row_idx_list[row_pin]].append(col_idx_list[col_pin])
            letter_dict[f'{row_idx_list[row_pin]}-{col_idx_list[col_pin]}'] = letter
            # 下次字母的坐标，先判定方向
            if row_pin + 1 == len(row_idx_list):
                row_dir = 'down'
            elif row_pin == 0:
                row_dir = 'up'
            if row_dir == 'up':
                row_pin += 1
            else:
                row_pin -= 1
            # 纵坐标
            if col_pin + 1 == len(col_idx_list):
                col_pin = 0
                col_idx_list = [item + col_base for item in col_idx_list]
            else:
                col_pin += 1
        # output
        all_rows = list(read_dict.keys())
        all_rows.sort()
        output = []
        for row_idx in all_rows:
            for col_idx in read_dict[row_idx]:
                output.append(letter_dict[f'{row_idx}-{col_idx}'])
        return ''.join(output)
```

## 5. 盛最多水的容器 <a id="largest_area">📌</a>

> 给定一个长度为` n `的整数数组` height `。有` n `条垂线，第` i `条线的两个端点是` (i, 0) `和` (i, height[i]) `。
>
> 找出其中的两条线，使得它们与` x `轴共同构成的容器可以容纳最多的水。
>
> 返回容器可以储存的最大水量。
>
> **说明**：你不能倾斜容器。
>
> **示例1**：
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220427201803783.png" width="60%" />
> </div>
>
> ```
> 输入：[1,8,6,2,5,4,8,3,7]
> 输出：49 
> 解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
> ```
>
> **示例 2**：
>
> ```
> 输入：height = [1,1]
> 输出：1
> ```
>
> **提示**：
>
> - `n == height.length`
> - `2 <= n <= 10^5`
> - `0 <= height[i] <= 10^4`

**解题要点**：这种看起来可以用双循环解的问题，应当首要想起<u>双指针</u>法。双指针法的精髓在于可以以$O(N)$的复杂度遍历序列中的所有两两组合。本题的双指针解析如下：

- 分别设置左右指针，位于列表的首项与末项
- 每次都移动<u>指向较小数字</u>的一个指针。
- 上述做法基于一个事实，设`height[l]<height[r]`，且右指针只能向左移动、左指针只能向右移动。若固定较小的指针`height[l]`，则无论右指针如何移动，围成的面积都`<=(r - l) * height[l]`
    - 右指针向左移动，则`r - l`必然会下降
    - 若右指针移动到`r1`位置
        - `height[r] < height[r1]`，`area = (r1 - l) * height[l]`
        - `height[r] >= height[r1]`,`area = (r1 - l) * height[r1] < (r1 - l) * height[l]`

**优化答案**：

```python
class Solution:
    def get_area(self, idx1, idx2, height):
        a = height[idx1]
        b = height[idx2]
        return abs(idx1 - idx2) * min([a, b])

    def maxArea(self, height: List[int]) -> int:
        left_pin, right_pin = 0, len(height) - 1
        largest_area = self.get_area(0, 1, height)
        while left_pin < right_pin:
            area = self.get_area(left_pin, right_pin, height)
            if area > largest_area:
                largest_area = area
            if height[left_pin] <= height[right_pin]:
                left_pin += 1
            else: 
                right_pin -= 1
        return largest_area
```



## 6. 整数转罗马数字

> 罗马数字包含以下七种字符：` I`，` V`，` X`，` L`，`C`，`D` 和 `M`。
>
> 字符          数值
> I             1
> V             5
> X             10
> L             50
> C             100
> D             500
> M             1000
>
> 例如， 罗马数字 2写做` II `，即为两个并列的1。12写做` XII `，即为` X + II `。27写做`  XXVII`, 即为` XX + V + II` 。
>
> 通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如` 4 `不写做` IIII`，而是 `IV`。数字` 1 `在数字` 5 `的左边，所表示的数等于大数` 5` 减小数` 1 `得到的数值` 4 `。同样地，数字 `9 `表示为` IX`。这个特殊的规则只适用于以下六种情况：
>
> `I `可以放在 `V `(5) 和 `X` (10) 的左边，来表示 4 和 9。
> `X `可以放在` L` (50) 和 `C `(100) 的左边，来表示 40 和 90。 
> `C `可以放在 `D` (500) 和 `M` (1000) 的左边，来表示 400 和 900。
> 给你一个整数，将其转为罗马数字。
>
>  
>
> **示例 1**:
>
> ```
> 输入: num = 3
> 输出: "III"
> ```
>
> **示例 2**:
>
> ```
> 输入: num = 4
> 输出: "IV"
> ```
>
> **示例 3**:
>
> ```
> 输入: num = 9
> 输出: "IX"
> ```
>
> **示例 4**:
>
> ```
> 输入: num = 58
> 输出: "LVIII"
> 解释: L = 50, V = 5, III = 3.
> ```
>
> **示例 5**:
>
> ```
> 输入: num = 1994
> 输出: "MCMXCIV"
> 解释: M = 1000, CM = 900, XC = 90, IV = 4.
> ```
>
> **提示**：
>
> - `1 <= num <= 3999`

**解题思路**：数字转罗马字的思路为，对数字`num`寻找不超过该数的符号表示，然后`num`减去可被符号表示的部分，直到`num`为0。

**复习一下罗马字转数字的原理**：对任意罗马字符串`string`，当`string[i] < string[i+1]`

时，`string[i]`表示的整数应被视为<u>负数</u>；反之，若`string[i] >= string[i+1]`，`string[i]`表示的整数应被视为<u>正数</u>。

**原accept答案**：

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        key_list = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
        match_dict = {'1000': 'M', '500': 'D', '100': 'C', '50': 'L', '10': 'X', '5': 'V', '1': 'I',\
                    '900': 'CM', '400': 'CD', '90': 'XC', '40': 'XL', '4': 'IV', '9': 'IX'}
        output = []
        idx = 0
        while num > 0:
            n = num // key_list[idx]
            if n > 0:
                output = output + [match_dict[str(key_list[idx])]] * n
                num = num % key_list[idx]
            idx += 1
        return ''.join(output)
```

## 7. 三数之和 <a id="three_sum">📌</a>

> 给你一个包含` n `个整数的数组` nums`，判断` nums `中是否存在三个元素` a`，`b`，`c `，使得` a + b + c = 0 `？请你找出所有和为 0 且不重复的三元组。
>
> 注意：答案中不可以包含重复的三元组。
>
>  
>
> **示例 1**：
>
> ```
> 输入：nums = [-1,0,1,2,-1,-4]
> 输出：[[-1,-1,2],[-1,0,1]]
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = []
> 输出：[]
> ```
>
> **示例 3**：
>
> ```
> 输入：nums = [0]
> 输出：[]
> ```
>
> **提示**：
>
> - `0 <= nums.length <= 3000`
> - `-10^5 <= nums[i] <= 10^5`

**优化思路**：本题使用<u>排序+双指针</u>法，要点总结如下

- 保证`num1 <= num2 <= num3`，因此使用排序
- 固定`num1`，`num2`和`num3`指针分别位于剩余序列的最左侧和最有侧，`num2`指针向右移动的时候，`num3`指针可以在上一次移动的基础上继续向左移动（原因在于上一要点中的大小关系）
- 同一个指针移动时的要点在于：本次指向的数字不能和上次指向的数字相同
- **经过实验：少在循环中调用即使很简单的函数，而是用常数代替，可以大幅度减少运行时间**

**优化答案**：

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        if n < 3:
            return []
        nums.sort()
        output = []
        for num1_idx in range(n - 2):
            if num1_idx > 0 and nums[num1_idx] == nums[num1_idx - 1]:
                # num1指针仅指向第一个位置，或者任何与上一个值不相等的值上。
                continue
            # 双指针：
            # NOTE: num2_idx只能向右移动，num3_idx只能向左移动，下面这句放在第二个循环外，使得每次num2_idx向右移动的时候，num_idx都只能从上次的地方接着移动，这大幅度减少了运行时间，且符合要求：固定num1的情况下，num2增大，num3只能减小
            num3_idx = n - 1
            for num2_idx in range(num1_idx+1, n-1):
                if num2_idx > num1_idx+1 and nums[num2_idx] == nums[num2_idx - 1]:
                    # num2指针仅指向第一个位置，或者任何与上一个值不相等的值上。
                    continue
                # 保证指针顺序，使第三个指针指向使三个数之和小于等于0的位置上
                while num2_idx < num3_idx and nums[num2_idx] + nums[num3_idx] > 0 - nums[num1_idx]:
                    num3_idx -= 1
                if num2_idx >= num3_idx:
                    break
                # 此时有两种情况，小于0、等于0
                if nums[num2_idx] + nums[num3_idx] + nums[num1_idx] == 0:
                    output.append([nums[num1_idx], nums[num2_idx], nums[num3_idx]])
        return output
```

## 8. 最接近的三数之和 <a id="closest_target">📌</a>

> 给你一个长度为` n `的整数数组` nums `和 一个目标值` target`。请你从` nums `中选出三个整数，使它们的和与` target `最接近。
>
> 返回这三个数的和。
>
> 假定每组输入只存在恰好一个解。
>
>  
>
> **示例 1**：
>
> ```
> 输入：nums = [-1,2,1,-4], target = 1
> 输出：2
> 解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [0,0,0], target = 1
> 输出：0
> ```
>
> **提示**：
>
> - `3 <= nums.length <= 1000`
> - `-1000 <= nums[i] <= 1000`
> - `-10^4 <= target <= 10^4`

**解题思路**：本题和上一题一样，仍然是<u>排序+双指针思路</u>，这里记住不要被代码模版，双指针精髓在于<u>根据题目目标，指定指针移动方向</u>，以及最优值更新策略。

**accept答案**：

```python
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        nums.sort()
        output = 1e8
        n = len(nums)
        for num1_idx in range(n-2):
            if num1_idx > 0 and nums[num1_idx] == nums[num1_idx - 1]:
                continue
            num1 = nums[num1_idx]
            # 双指针
            num2_idx = num1_idx + 1
            num3_idx = n - 1
            while num2_idx < num3_idx:
                res = nums[num1_idx] + nums[num2_idx] + nums[num3_idx]
                # 对现在指针指向的位置做评估
                if res == target:
                    return target
                if abs(output - target) > abs(res - target):
                    output = res
                # 为下次判断移动指针，向更接近target的方向移动
                if res < target:
                    # 移动到下一个不重复的数
                    num2_idx_new = num2_idx + 1
                    while num2_idx_new < num3_idx and nums[num2_idx_new] == nums[num2_idx]:
                        num2_idx_new += 1
                    num2_idx = num2_idx_new
                else:
                    # 移动到下一个不重复的数
                    num3_idx_new = num3_idx - 1
                    while num2_idx < num3_idx_new and nums[num3_idx_new] == nums[num3_idx]:
                        num3_idx_new -= 1
                    num3_idx = num3_idx_new
        return output
```

## 9.电话号码的字母组合<a id="comb_phonenumber">📌</a>

> 给定一个仅包含数字` 2-9 `的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。
>
> 给出数字到字母的映射如下（与电话按键相同）。注意` 1 `不对应任何字母。
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220428204212998.png" alt="image-20220428204212998" width="30%"/>
> </div> 
>
> **示例 1**：
>
> ```
> 输入：digits = "23"
> 输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
> ```
>
> **示例 2**：
>
> ```
> 输入：digits = ""
> 输出：[]
> ```
>
> **示例 3**：
>
> ```
> 输入：digits = "2"
> 输出：["a","b","c"]
> ```
>
> **提示**：
>
> - `0 <= digits.length <= 4`
> - `digits[i] `是范围` ['2', '9'] `的一个数字。



**原accept答案**：

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if len(digits) == 0:
            return []
        number_dict = {'2': ['a', 'b', 'c'], \
                       '3': ['d', 'e', 'f'], \
                       '4': ['g', 'h', 'i'], \
                       '5': ['j', 'k', 'l'], \
                       '6': ['m', 'n', 'o'], \
                       '7': ['p', 'q', 'r', 's'], \
                       '8': ['t', 'u', 'v'], 
                       '9': ['w', 'x', 'y', 'z']}
        tmp = [] # list of letter
        for num in digits:
            tmp.append(number_dict[num])
        # 输入‘234’，问题可分解为代表‘4’的字母排列分别和代表字母‘3’的字母做排列
        def output_list(list1, list2):
            # list1: ['a', 'b', 'c']
            # list2: ['d', 'e', 'f']
            output = []
            for i in list1:
                for j in list2:
                    output.append(''.join([i, j]))
            return output
        num_list = len(tmp)
        output = tmp[0]
        for idx in range(1, num_list):
            output = output_list(output, tmp[idx])
        return output
```

**优化思路**：回溯——回溯算法用于寻找所有的可行解，如果发现一个解不可行，则会舍弃不可行的解。在这道题中，由于每个数字对应的每个字母都可能进入字母组合，因此不存在不可行的解，直接穷举所有的解即可。

**优化答案**：

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if len(digits) == 0:
            return []
        number_dict = {'2': ['a', 'b', 'c'], \
                       '3': ['d', 'e', 'f'], \
                       '4': ['g', 'h', 'i'], \
                       '5': ['j', 'k', 'l'], \
                       '6': ['m', 'n', 'o'], \
                       '7': ['p', 'q', 'r', 's'], \
                       '8': ['t', 'u', 'v'], 
                       '9': ['w', 'x', 'y', 'z']}
        # 回溯，有点像深度遍历树
        def backtrack(idx):
            # idx可以理解为树的层级
            if idx == len(digits):
                output.append(''.join(stack))
            else:
                num = digits[idx]
                for letter in number_dict[num]:
                    stack.append(letter)
                    backtrack(idx+1)
                    # 上一句得到返回的时候，代表第idx层已经深度遍历完毕，删除最后一次的路径
                    stack.pop()

        output = [] # 存储所有结果
        stack = [] # 存储一次遍历路径
        backtrack(0)
        return output
```

## 10. 整数反转

> 给你一个 32 位的有符号整数` x `，返回将` x `中的数字部分反转后的结果。
>
> 如果反转后整数超过 32 位的有符号整数的范围` [−2^31,  2^31 − 1] `，就返回 0。
>
> **假设环境不允许存储 64 位整数（有符号或无符号）。**
>
> **示例 1**：
>
> ```
> 输入：x = 123
> 输出：321
> ```
>
> **示例 2**：
>
> ```
> 输入：x = -123
> 输出：-321
> ```
>
> **示例 3**：
>
> ```
> 输入：x = 120
> 输出：21
> ```
>
> **示例 4**：
>
> ```
> 输入：x = 0
> 输出：0
> ```
>
> **提示**：
>
> - `-2^31 <= x <= 2^31 - 1`

**解题要点**：假设环境不允许存储64位整数，即需要我们在乘10操作之前进行判断

**accept答案**：

```python
class Solution:
    def reverse(self, x: int) -> int:
        is_neg = True if x < 0 else False
        x = abs(x)

        output = 0 
        while x != 0:
            if (is_neg is False and output > (2**31-1) // 10) or \
               (is_neg is True and output > (2**31) // 10):
                # 超出了表示范围
                return 0
            low = x % 10 # 最低位
            x = x // 10
            output = output * 10 + low

        if is_neg: output = - output
        return output
```

## 11. 字符串转整数（atoi）

> 请你来实现一个` myAtoi(string s) `函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的` atoi `函数）。
>
> 函数` myAtoi(string s) `的算法如下：
>
> 1. 读入字符串并丢弃无用的前导空格
> 2. 检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则假定结果为正。
> 3. 读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。
> 4. 将前面步骤读入的这些数字转换为整数（即，`"123" `-> `123`，` "0032"` ->` 32`）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。
> 5. 如果整数数超过 32 位有符号整数范围` [−2^31,  2^31 − 1]` ，需要截断这个整数，使其保持在这个范围内。具体来说，小于` −2^31 `的整数应该被固定为` −2^31` ，大于1的整数应该被固定为` 2^31 − 1 `。
> 6. 返回整数作为最终结果。
>
> **注意**：
>
> - 本题中的空白字符只包括空格字符` ' '` 。
> - 除前导空格或数字后的其余字符串外，请勿忽略 任何其他字符。
>
> **示例 1**：
>
> ```
> 输入：s = "42"
> 输出：42
> 解释：加粗的字符串为已经读入的字符，插入符号是当前读取的字符。
> 第 1 步："42"（当前没有读入字符，因为没有前导空格）
>          ^
> 第 2 步："42"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
>          ^
> 第 3 步："42"（读入 "42"）
>            ^
> 解析得到整数 42 。
> 由于 "42" 在范围 [-231, 231 - 1] 内，最终结果为 42 。
> ```
>
>
> **示例 2**：
>
> ```
> 输入：s = "   -42"
> 输出：-42
> 解释：
> 第 1 步："   -42"（读入前导空格，但忽视掉）
>             ^
> 第 2 步："   -42"（读入 '-' 字符，所以结果应该是负数）
>              ^
> 第 3 步："   -42"（读入 "42"）
>                ^
> 解析得到整数 -42 。
> 由于 "-42" 在范围 [-231, 231 - 1] 内，最终结果为 -42 。
> ```
>
> **示例 3**：
>
> ```
> 输入：s = "4193 with words"
> 输出：4193
> 解释：
> 第 1 步："4193 with words"（当前没有读入字符，因为没有前导空格）
>          ^
> 第 2 步："4193 with words"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
>          ^
> 第 3 步："4193 with words"（读入 "4193"；由于下一个字符不是一个数字，所以读入停止）
>              ^
> 解析得到整数 4193 。
> 由于 "4193" 在范围 [-231, 231 - 1] 内，最终结果为 4193 。
> ```
>
> **提示**：
>
> - `0 <= s.length <= 200`
> - `s `由英文字母（大写和小写）、数字（0-9）、`' '`、`'+'`、`'-'` 和` '.' `组成

**原accept代码**：

```python
class Solution:
    def myAtoi(self, s: str) -> int:
        # 丢弃前导空格
        s = s.lstrip(' ')
        output = 0
        is_neg = False
        for i, item in enumerate(s):
            if i == 0:
                try: 
                    output = int(item)
                except:
                    if item == '-':
                        is_neg = True
                    elif item == '+':
                        is_neg = False
                    else:
                        return 0
            else:
                # 判断是否读入非数字字符
                try:
                    output = output * 10 + int(item)
                except:
                    break
        output = - output if is_neg else output
        if output > 2**31 - 1: output = 2**31 - 1
        elif output < - (2**31): output = - (2**31)
        return output
```

**解题思路**：<u>确定有限状态机（deterministic finite automaton, DFA）</u>，不太清楚干嘛要用这么复杂的方法，以后可以看看。

## 12. 四数之和 <a id="four_sum">📌</a>

> 给你一个由` n `个整数组成的数组` nums `，和一个目标值` target `。请你找出并返回满足下述全部条件且不重复的四元组` [nums[a], nums[b], nums[c], nums[d]] `（若两个四元组元素一一对应，则认为两个四元组重复）：
>
> - `0 <= a, b, c, d < n`
> - `a`、`b`、`c `和` d `互不相同
> - `nums[a] + nums[b] + nums[c] + nums[d] == target`
> - 你可以按 **任意顺序** 返回答案 。
>
>  
>
> **示例 1**：
>
> ```
> 输入：nums = [1,0,-1,0,-2,2], target = 0
> 输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [2,2,2,2,2], target = 8
> 输出：[[2,2,2,2]]
> ```
>
> **提示**：
>
> - `1 <= nums.length <= 200`
> - `-10^9 <= nums[i] <= 10^9`
> - `-10^9 <= target <= 10^9`

**解题思路**：本题的思路与 [三数之和](#three_sum) 一样，即“<u>排序+双指针</u>”，注意可以考虑以下剪枝操作：

- 确定`num1 = nums[p1]`后，若`nums[p1] + ... + nums[p1 + 3] > target`，即最小数的组合都大于`target`，则可以跳出第一个循环。
- 同理，`num1 = nums[p1]`后，若`nums[p1] + nums[n - 1] + ... + nums[n - 3] < target`，即最大数的组合都小于`target`，则可以跳出第一个循环。
- 上述规则也适用于第二个循环。

**accept代码**：

```python
class Solution:
    def move_pin(self, nums, p, direction):
        if direction == '+':
            p_new = p + 1
            if nums[p_new] == nums[p_new - 1]:
                p_new += 1
            p_out = p_new
        else:
            p_new = p - 1
            if nums[p_new] == nums[p_new + 1]:
                p_new -= 1
            p_out = p_new   
        return p_out     
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        n = len(nums)
        if n < 4:
            return []
        output = []
        nums.sort()
        for p1 in range(n - 3):
            if p1 > 0 and nums[p1] == nums[p1 - 1]:
                continue
            if nums[p1] + nums[p1 + 1] + nums[p1 + 2] + nums[p1 + 3] > target:
                continue
            if nums[p1] + nums[n - 1] + nums[n - 2] + nums[n - 3] < target:
                continue
            n1 = nums[p1]
            for p2 in range(p1 + 1, n - 2):
                if p2 > p1 + 1 and nums[p2] == nums[p2 - 1]:
                    continue
                if nums[p1] + nums[p2] + nums[p2 + 1] + nums[p2 + 2] > target:
                    continue
                if nums[p1] + nums[p2] + nums[n - 1] + nums[n - 2] < target:
                    continue
                n2 = nums[p2]
                # 双指针
                p3 = p2 + 1
                p4 = n - 1
                while p3 < p4:
                    if n1 + n2 + nums[p3] + nums[p4] == target:
                        if [n1, n2, nums[p3], nums[p4]] not in output:
                            output.append([n1, n2, nums[p3], nums[p4]])
                        # p3 p4各自移动一次
                        p3 = self.move_pin(nums, p3, '+')
                        p4 = self.move_pin(nums, p4, '-')
                    elif n1 + n2 + nums[p3] + nums[p4] < target:
                        p3 = self.move_pin(nums, p3, '+')
                    else:
                        p4 = self.move_pin(nums, p4, '-')
        return output
```

## 13. 删除链表的倒数第N个节点

> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。
>
> **示例 1：**
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220429161243362.png" alt="image-20220429161243362" width="50%" />
> </div>
>
> ```
> 输入：head = [1,2,3,4,5], n = 2
> 输出：[1,2,3,5]
> ```
>
> **示例 2**：
>
> ```
> 输入：head = [1], n = 1
> 输出：[]
> ```
>
> **示例 3**：
>
> ```
> 输入：head = [1,2], n = 1
> 输出：[1]
> ```
>
> **提示**：
>
> - 链表中结点的数目为` sz`
> - `1 <= sz <= 30`
> - `0 <= Node.val <= 100`
> - `1 <= n <= sz`
>
> **进阶**：你能尝试使用一趟扫描实现吗？

**原accept代码**：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        # 思路：如果想一次扫描，则在每次扫描到新节点的时候，都用两个指针分别指向要删除的节点之前和之后的节点。
        # 只要遍历节点到最后一个即可
        if head.next is None and n == 1:
            return None
        output_head = head
        node_idx = 0 # 本次遍历到的节点的索引
        node_list = []
        while head is not None:
            # 本次遍历到的节点索引 node_idx
            node_list.append(head)
            delete_idx = node_idx - n + 1
            if delete_idx >= 0:
                # 存在一个要删除的节点了
                delete_font = delete_idx - 1
                delete_rear = delete_idx + 1
            # 遍历下一个
            head = head.next
            node_idx += 1
        sz = len(node_list)
        if delete_rear < sz and delete_font >= 0:
            node_list[delete_font].next = node_list[delete_rear]
        elif delete_rear >= sz:
            node_list[delete_font].next = None
        elif delete_font < 0:
            return node_list[delete_rear]
        return output_head
```

**解题思路**：如果想一次扫描解决改问题，则在每次扫描到新节点的时候，都用两个指针分别指向要删除的节点之前和之后的节点。然后等待算法遍历到最后一个节点即可。

**优化思路**：

- 可以不借助列表存储Node，仅借助指针指向要删除节点的前驱节点
- 可以设置哑节点（dummy），该节点指向头指针，这样就无需对头指针做特殊的判断。

**优化答案**：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(-1, head)
        # init
        second = dummy # 指向要删除节点的前驱节点，即与first相聚n个节点
        for i in range(n):
            first = head
            head = head.next
        while head is not None:
            first = head
            second = second.next
            head = head.next
        # 删除节点
        second.next = second.next.next
        return dummy.next
```

## 14. 括号生成

> 数字` n `代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。
>
> **示例 1**：
>
> ```
> 输入：n = 3
> 输出：["((()))","(()())","(())()","()(())","()()()"]
> ```
>
> **示例 2**：
>
> ```
> 输入：n = 1
> 输出：["()"]
> ```
>
> **提示**：
>
> - `1 <= n <= 8`

**解题思路**：本题首先一定要用到递归，重点在于问题分解。本题的分解思路为：

生成包含`n`个括号的问题`gp(n)`，其基本形式为`(a)b`，`a`, `b`分别代表一个有效的括号序列，`a`, `b`可以为空。

**accept答案**：

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        # gp(n)可以分解为基本形式:(a)b
        # a, b分别代表一个有效的括号序列，a, b可以为空
        def gen(num):
            if num == 1:
                return ['()']
            elif num == 0:
                return ['']
            this_output = []
            for a in range(0, num):
                b = num - a - 1
                a_seq = gen(a)
                b_seq = gen(b)
                for item_a in a_seq:
                    for item_b in b_seq:
                        this_output.append(f'({item_a}){item_b}')
            this_output = list(set(this_output))
            return this_output
        return gen(n)
```

## 15. 两两交换列表中的节点

> 给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。
>
>  <div align="center">
>    <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220505235347394.png" alt="image-20220505235347394" width="40%" />
>   </div>
>
>
> 
>
> 
>
> **示例 1**：
>
> ```
> 输入：head = [1,2,3,4]
> 输出：[2,1,4,3]
> ```
>
> **示例 2**：
>
> ```
> 输入：head = []
> 输出：[]
> ```
>
> **示例 3**：
>
> ```
> 输入：head = [1]
> 输出：[1]
> ```
>
> **提示**：
>
> - 链表中节点的数目在范围` [0, 100] `内
> - `0 <= Node.val <= 100`

**解题思路**：定义哑节点，梳理节点的迭代交换步骤，并写出通用的迭代步骤

**原accept答案**：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:
            return head
        dumb = ListNode(-1, head)
        first = dumb
        second = dumb.next
        while second and first and second.next:
            first.next = second.next
            second.next = second.next.next
            first.next.next = second
            # next step
            first = first.next.next
            second = first.next
        return dumb.next
```

## 16. 两数相除 <a id="two_divide">📌</a>

> 给定两个整数，被除数` dividend `和除数` divisor`。将两数相除，要求不使用乘法、除法和` mod `运算符。
>
> 返回被除数` dividend `除以除数` divisor `得到的商。
>
> 整数除法的结果应当截去（truncate）其小数部分，例如：`truncate(8.345) = 8 `以及 `truncate(-2.7335) = -2`
>
> **示例 1**:
>
> ```
> 输入: dividend = 10, divisor = 3
> 输出: 3
> 解释: 10/3 = truncate(3.33333..) = truncate(3) = 3
> ```
>
> **示例 2**:
>
> ```
> 输入: dividend = 7, divisor = -3
> 输出: -2
> 解释: 7/-3 = truncate(-2.33333..) = -2
> ```
>
> 
>
> **提示**：
>
> - 被除数和除数均为` 32 `位有符号整数。
> - 除数不为` 0`。
> - 假设我们的环境只能存储 32 位有符号整数，其数值范围是` [−2^31,  2^31 − 1]`。本题中，如果除法结果溢出，则返回` 2^31 − 1`。

**解题思路**：二分法查找使得`res * divisor <= dividend < (res + 1) * divisor`的数

**accept答案**：

```python
class Solution:
    def mul(self, x, y):
        # x * y
        res = 0
        while y > 0:
            if (y & 1) == 1:
                # y的最低位
                res += x # 此时最低位代表加一次x
            # next loop
            y >>= 1 # y向右移动一位
            x += x # y向右移动之后，x所代表的含义要翻倍
        return res
    def divide(self, dividend: int, divisor: int) -> int:
        int_max = 2 ** 31 - 1
        int_min = - 2 ** 31
        # 边界情况
        if dividend == int_min and divisor == -1:
            return int_max
        if dividend == 0:
            return 0
    
        if (dividend > 0 and divisor > 0) or (dividend < 0 and divisor < 0):
            is_neg = False
        else:
            is_neg = True
        dividend, divisor = abs(dividend), abs(divisor)
        # 二分法查找使得res * divisor <= dividend < (res + 1) * divisor的数
        # res in [0, dividend]
        left, right = 0, dividend
        while left < right:
            # mid = (left + right) / 2
            mid = (left + right + 1) >> 1
            # if mid * divisor > dividend
            if self.mul(divisor, mid) <= dividend:
                # NOTE: mid中的//与>>1都是向下取整的操作，所以在移动左指针的时候要加1，防止死循环
                left = mid
            else:
                right = mid - 1
        output = - left if is_neg else left
        return output
```



## 17. 全排列

> 给定一个不含重复数字的数组 `nums `，返回其 **所有可能的全排列** 。你可以 **按任意顺序** 返回答案。 
>
> **示例 1**：
>
> ```
> 输入：nums = [1,2,3]
> 输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [0,1]
> 输出：[[0,1],[1,0]]
> ```
>
> **示例 3**：
>
> ```
> 输入：nums = [1]
> 输出：[[1]]
> ```
>
> **提示**：
>
> - `1 <= nums.length <= 6`
> - `-10 <= nums[i] <= 10`
> - `nums `中的所有整数 **互不相同**

**解题思路**：全排列问题可以分解为，列表中的每个不想等的元素做开头，+ 其余元素的全排列

**原accept代码**：

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        # 原函数：返回一个列表的全排列
        # 递归：一个列表的全排列，是其中每个元素做首位，其余元素全排列的结果
        if len(nums) == 1:
            return [nums]
        else:
            output = []
            for i, item in enumerate(nums):
                # 以item为首位
                if i != len(nums) - 1:
                    tmp_res = self.permute(nums[0:i] + nums[i+1:])
                else: 
                    tmp_res = self.permute(nums[0:i])
                # tmp_res 其他元素的全排列
                output += [[item] + res for res in tmp_res]
            return output
```

## 18. 全排列II

> 给定一个可包含重复数字的序列` nums `，按**任意顺序** 返回所有不重复的全排列。
>
> **示例 1**：
>
> ```
> 输入：nums = [1,1,2]
> 输出：
> [[1,1,2],
>  [1,2,1],
>  [2,1,1]]
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [1,2,3]
> 输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
> ```
>
> **提示**：
>
> - `1 <= nums.length <= 8`
> - `-10 <= nums[i] <= 10`

**解题思路**：原问题分解为，以每个不相同的元素做开头，+ 其余元素的全排列

**原accept答案**：

```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        # 分解：以每个不相同的元素做开头，+ 其余元素的全排列
        if len(nums) == 1:
            return [nums]
        else:
            output = []
            unique_item = list(set(nums))
            for item in unique_item:
                # 构造其余元素构成的列表
                i = nums.index(item)
                if i != len(nums) - 1:
                    others = nums[0:i] + nums[i+1:]
                else:
                    others = nums[0:i]
                # 全排列
                tmp_res = self.permuteUnique(others)
                output += [[item] + res for res in tmp_res]
            return output
                
```

## 19. 旋转图像

> 给定一个` n × n `的二维矩阵` matrix `表示一个图像。请你将图像顺时针旋转 90 度。
>
> 你必须在 **原地** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。
>
> **示例 1：**
>
> <div align="center">
> <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/20220512221234.png" width="50%" />
> </div>
>
> ```
> 输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
> 输出：[[7,4,1],[8,5,2],[9,6,3]]
> ```
>
> **示例 2：**
>
> <div align="center">
> <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220512221337369.png" width="50%" />
> </div>
>
> ```
> 输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
> 输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
> ```
>
> **提示**：
>
> - `n == matrix.length == matrix[i].length`
> - `1 <= n <= 20`
> - `-1000 <= matrix[i][j] <= 1000`

**解题思路**：观察规律：新矩阵的第`i`行是旧矩阵的第`i`列的倒序，即`new[i][j] = ori[n-j-1][i]`

**原accept答案**：

```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        n = len(matrix)
        # 暂存
        ori = []
        for i in range(n):
            ori.append([])
            for j in range(n):
                ori[i].append(matrix[i][j])
        # 新矩阵的第i行是旧矩阵的第i列的倒序
        # new[i][j] = ori[n-j-1][i]
        for i in range(n):
            for j in range(n):
                matrix[i][j] = ori[n-j-1][i]
```

## 20. Pow(x, n)

> 实现` pow(x, n) `，即计算` x `的` n `次幂函数（即，`x^n `）。
>
> **示例 1**：
>
> ```
> 输入：x = 2.00000, n = 10
> 输出：1024.00000
> ```
>
> **示例 2**：
>
> ```
> 输入：x = 2.10000, n = 3
> 输出：9.26100
> ```
>
> 示例 3：
>
> ```
> 输入：x = 2.00000, n = -2
> 输出：0.25000
> 解释：2-2 = 1/22 = 1/4 = 0.25
> ```
>
> **提示**：
>
> - `-100.0 < x < 100.0`
> - `-2^31 <= n <= 2^31-1`
> - `-10^4 <= x^n <= 10^4`

**解题思路**：先倍增，后调整

**原accept答案**：

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        elif n < 0:
            x = 1 / x
            n = - n
        # 倍增
        cnt = 1
        res = x
        while cnt < n:
            res *= res
            cnt *= 2
        # 调整
        if cnt == n:
            return res
        else: 
            while cnt > n:
                res = res / x
                cnt -= 1
            return res
```

## 21. 字母异位词分组

> 给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。
>
> **字母异位词** 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。
>
>  
>
> **示例 1**:
>
> ```
> 输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
> 输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
> ```
>
> **示例 2**:
>
> ```
> 输入: strs = [""]
> 输出: [[""]]
> ```
>
> **示例 3**:
>
> ```
> 输入: strs = ["a"]
> 输出: [["a"]]
> ```
>
> **提示**：
>
> - `1 <= strs.length <= 10^4`
> - `0 <= strs[i].length <= 100`
> - `strs[i] `仅包含小写字母

**解题思路**：简单，但注意提示中的`strs.length`很大，因此只能循环一次。

**原accept答案**：

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        if len(strs) == 1:
            return [strs]
        output = {}
        for word in strs:
            key = list(word)
            key.sort()
            key = ''.join(key)
            if output.get(key) is None:
                output[key] = []
            output[key].append(word)
        return list(output.values())
```

## 22. 字符串相乘

> 给定两个以字符串形式表示的非负整数` num1 `和` num2`，返回` num1 `和` num2 `的乘积，它们的乘积也表示为字符串形式。
>
> **注意**：不能使用任何内置的` BigInteger `库或直接将输入转换为整数。
>
> **示例 1**:
>
> ```
> 输入: num1 = "2", num2 = "3"
> 输出: "6"
> ```
>
> **示例 2**:
>
> ```
> 输入: num1 = "123", num2 = "456"
> 输出: "56088"
> ```
>
> **提示**：
>
> - `1 <= num1.length, num2.length <= 200`
> - `num1 `和` num2 `只能由数字组成。
> - `num1 `和 `num2 `都不包含任何前导零，除了数字`0`本身。

**解题思路**：纯练手题，没什么意义

**原accept答案**：

```python
class Solution:
    def multiply(self, num1: str, num2: str) -> str:
        if num1 == '0' or num2 == '0':
            return '0'
        def num2str(num):
            output = ''
            while num != 0:
                tmp = num % 10
                output = str(tmp) + output
                num = num // 10
            return output
        def str2num(strs):
            output = 0
            for num in strs:
                output = output * 10 + int(num)
            return output
        return num2str(str2num(num1) * str2num(num2))
```

## 23. 组合总和 <a id="comb_sum">📌</a>

> 给你一个 **无重复元素** 的整数数组` candidates `和一个目标整数` target `，找出` candidates `中可以使数字和为目标数` target `的 **所有** 不同组合 ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。
>
> `candidates `中的 同一个 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 
>
> 对于给定的输入，保证和为` target `的不同组合数少于 150 个。
>
> **示例 1**：
>
> ```
> 输入：candidates = [2,3,6,7], target = 7
> 输出：[[2,2,3],[7]]
> 解释：
> 2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
> 7 也是一个候选， 7 = 7 。
> 仅有这两种组合。
> ```
>
> 示例 2：
>
> ```
> 输入: candidates = [2,3,5], target = 8
> 输出: [[2,2,2,2],[2,3,3],[3,5]]
> ```
>
> 示例 3：
>
> ```
> 输入: candidates = [2], target = 1
> 输出: []
> ```
>
> **提示**：
>
> - `1 <= candidates.length <= 30`
> - `1 <= candidates[i] <= 200`
> - `candidate `中的每个元素都 **互不相同**
> - `1 <= target <= 500`

解题思路：可以用回溯来解决这个问题。<u>问题分解</u>：每次选取列表中的一个不一样的数字num，作为基础数字，问题分解为寻找` target - num`在`candidates`中的组合。

⚠️：回溯的时候可以选择全部遍历，在失败的时候需要及时**pop**出路径中的末尾结果。

⚠️：**这题应该在回溯的时候应该会有剪枝的方法，注意看看题解**

**原accept答案**：

```python
class Solution:
    def __init__(self):
        self.output = []
        
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        # 问题分解：每次选取列表中的一个不一样的数字num，作为基础数字
        # combinationSum(candidates, target - num)
        # 停止条件：target <= 0
        def flashBack(can, tar, path=[]):
            if tar == 0:
                return True
            elif tar < 0:
                # 失败迭代
                path.pop()
                return False
            else:
                i = 0 # 指针指向本次使用的数字
                while i < len(can):
                    # 将当前的数字添加到路径中
                    path.append(can[i])
                    res = flashBack(can, tar - can[i], path)
                    if res is True:
                        copy = [itm for itm in path]
                        copy.sort()
                        if copy not in self.output:
                            self.output.append(copy)
                        path.pop()
                    i += 1
                if len(path) > 0:
                    path.pop()
        flashBack(candidates, target)
        return self.output
```

## 24. 组合总和II <a id="comb_sum2">📌</a>

> 给定一个候选人编号的集合` candidates `和一个目标数` target `，找出` candidates `中所有可以使数字和为` target `的组合。
>
> `candidates `中的每个数字在每个组合中只能使用 一次 。
>
> **注意**：解集不能包含重复的组合。 
>
>  
>
> **示例 1**:
>
> ```
> 输入: candidates = [10,1,2,7,6,1,5], target = 8,
> 输出:
> [
> [1,1,6],
> [1,2,5],
> [1,7],
> [2,6]
> ]
> ```
>
> **示例 2**:
>
> ```
> 输入: candidates = [2,5,2,1,2], target = 5,
> 输出:
> [
> [1,2,2],
> [5]
> ]
> ```
>
> **提示**:
>
> - `1 <= candidates.length <= 100`
> - `1 <= candidates[i] <= 50`
> - `1 <= target <= 30`

**解题思路**：原问题可以做如下的分解

1. 以`candidates`中的每个不同的数`num`作为路径的开头，

2. 寻找和`target - num`在`candidates - nums`中的组合，查找完毕后，将`num`再次放进`candidates`

3. 停止条件：(1) `target == 0`: 迭代成功；(2) `target < 0`: 迭代失败；(3) `target > 0 and len(can) == 0`: 迭代失败。

⚠️：**在成功后，应该会有剪枝操作，注意看题解**

**原accept答案**：

```python
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        # 问题分解：
        # 1. candidates中的每个不同的数num作为路径的开头，
        # 2. 寻找和target - num在candidates - nums中的组合
        # 3. 停止条件：
        #   (1) target == 0: 迭代成功；
        #   (2) target < 0: 迭代失败；
        #   (3) target > 0 and len(can) == 0: 迭代失败。
        
        # 排序以寻找不相同的数字
        candidates.sort()
        output = []
        def backtrack(can, tar, path=[]):
            if tar == 0:
                return True
            elif tar < 0:
                path.pop()
                return False
            elif tar > 0 and len(can) == 0:
                path.pop()
                return False
            else:
                i = 0
                while i < len(can):
                    # pop can[i] from can
                    tmp = can.pop(i)
                    path.append(tmp)
                    res = backtrack(can, tar - tmp, path)
                    if res is True:
                        copy = [item for item in path]
                        copy.sort()
                        path.pop()
                        if copy not in output:
                            output.append(copy)
                    # i 位置的数字放回can中
                    can.insert(i, tmp)
                    # 指针移动到下一个不相同的数字上
                    while i+1 < len(can) and can[i+1] == can[i]:
                        i += 1
                    i += 1
                if len(path) > 0:
                    path.pop()
        backtrack(candidates, target)
        return output
```



## 25. 外观数列

> 给定一个正整数` n `，输出外观数列的第` n `项。
>
> 「外观数列」是一个整数序列，从数字` 1 `开始，序列中的每一项都是对前一项的描述。
>
> 你可以将其视作是由递归公式定义的数字字符串序列：
>
> `countAndSay(1) = "1"`
> `countAndSay(n) `是对` countAndSay(n-1) `的描述，然后转换成另一个数字字符串。
> 前五项如下：
>
> ```
> 1.     1
> 2.     11
> 3.     21
> 4.     1211
> 5.     111221
>        第一项是数字 1 
>        描述前一项，这个数是 1 即 “ 一 个 1 ”，记作 "11"
>        描述前一项，这个数是 11 即 “ 二 个 1 ” ，记作 "21"
>        描述前一项，这个数是 21 即 “ 一 个 2 + 一 个 1 ” ，记作 "1211"
>        描述前一项，这个数是 1211 即 “ 一 个 1 + 一 个 2 + 二 个 1 ” ，记作 "111221"
>        
> ```
>
> 要 **描述** 一个数字字符串，首先要将字符串分割为 最小 数量的组，每个组都由连续的**最多** 相同字符 组成。然后对于每个组，先描述字符的数量，然后描述字符，形成一个描述组。要将描述转换为数字字符串，先将每组中的字符数量用数字替换，再将所有描述组连接起来。
>
> 例如，数字字符串` "3322251"` 的描述如下图：
>
> <div align="center">
> <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220513115313797.png" alt="image-20220513115313797" width="45%" />
> </div>
>
> **示例 1**：
>
> ```
> 输入：n = 1
> 输出："1"
> 解释：这是一个基本样例。
> ```
>
> **示例 2**：
>
> ```
> 输入：n = 4
> 输出："1211"
> 解释：
> countAndSay(1) = "1"
> countAndSay(2) = 读 "1" = 一 个 1 = "11"
> countAndSay(3) = 读 "11" = 二 个 1 = "21"
> countAndSay(4) = 读 "21" = 一 个 2 + 一 个 1 = "12" + "11" = "1211"
> ```
>
> **提示**：
>
> - `1 <= n <= 30`

**解题思路**：很明显用递归即可，只是注意边界的处理

**原accept答案**：

```python
class Solution:
    def countAndSay(self, n: int) -> str:
        if n == 1:
            return '1'
        else:
            prev = self.countAndSay(n - 1) # 要描述的字符串
            count_list = [] # list in it, [n, m], n出现过m次
            i = 0
            tmp = [prev[i], 1]
            while i < len(prev):
                if i+1 < len(prev) and prev[i+1] == prev[i]:
                    tmp[1] += 1
                    i += 1
                else:
                    if i+1 < len(prev):
                        count_list.append(tmp)
                        tmp = [prev[i+1], 1]
                        i += 1
                    else:
                        break
            count_list.append(tmp)
            output = [f'{item[1]}{item[0]}' for item in count_list]
            return ''.join(output)
```

## 31. 下一个排列 <a id="next_comb">📌</a>

> 整数数组的一个 **排列** 就是将其所有成员以序列或线性顺序排列。
>
> - 例如，`arr = [1,2,3]` ，以下这些都可以视作 `arr` 的排列：`[1,2,3]`、`[1,3,2]`、`[3,1,2]`、`[2,3,1]` 。
>
> 整数数组的 **下一个排列** 是指其整数的下一个字典序更大的排列。更正式地，如果数组的所有排列根据其字典顺序从小到大排列在一个容器中，那么数组的 **下一个排列** 就是在这个有序容器中排在它后面的那个排列。如果不存在下一个更大的排列，那么这个数组必须重排为字典序最小的排列（即，其元素按升序排列）。
>
> - 例如，`arr = [1,2,3]` 的下一个排列是 `[1,3,2]` 。
> - 类似地，`arr = [2,3,1]` 的下一个排列是 `[3,1,2]` 。
> - 而 `arr = [3,2,1]` 的下一个排列是 `[1,2,3]` ，因为 `[3,2,1]` 不存在一个字典序更大的排列。
>
> 给你一个整数数组 `nums` ，找出 `nums` 的下一个排列。
>
> 必须**[ 原地 ](https://baike.baidu.com/item/原地算法)**修改，只允许使用额外常数空间。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [1,2,3]
> 输出：[1,3,2]
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [3,2,1]
> 输出：[1,2,3]
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [1,1,5]
> 输出：[1,5,1]
> ```
>
>  
>
> **提示：**
>
> - `1 <= nums.length <= 100`
> - `0 <= nums[i] <= 100`

accept答案：

```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        '''
        思路: 可以参考下列的例子，可以看出，找下一个排列的关键点在于
        1. 确定一个位置，我们只改变这个位置以及之右的字符串，使得排列更大
        2. 确定位置：从右到左，当一个数字 a[i] < a[i+1]的时候，其中较小的数字a[i]即为分界线(因为至少a[i+1]和a[i]交换就可以使得排列变大)
        3. 寻找交换目标：比a[i]更大的数字应该更靠右，所以从最右手边开始，寻找比a[i]大的数字a[j]
        4. 交换a[i], a[j]，交换后的数组一定变大了
        5. 确保数组是“下一个排列”，使得交换后的较小数字后的数列呈升序排列
        6. [trick] 在做第五步的时候，可以直接倒转较小数之右的数字（因为右边的数组一定是一个降序数列，被换位置之右的数字小于等于较小数，被换位置之左的数字大于等于较小数）
        '''
        # step 2
        small_idx, large_idx = None, None
        for i in range(len(nums)-2, -1, -1):
            if nums[i] < nums[i+1]:
                small_idx = i
                break
        # 没有下一个排列
        if small_idx is None:
            for i in range(0, len(nums)//2):
                tmp = nums[i]
                nums[i] = nums[len(nums)-i-1]
                nums[len(nums)-i-1] = tmp
            return nums
        # step 3
        for i in range(len(nums)-1, small_idx, -1):
            if nums[i] > nums[small_idx]:
                large_idx = i
                break
        # step4 4: swap
        small, large = nums[small_idx], nums[large_idx]
        nums[small_idx] = large
        nums[large_idx] = small
        # step 5/6: reverse
        i = small_idx+1
        j = len(nums)-1
        while i < j:
            tmp = nums[i]
            nums[i] = nums[j]
            nums[j] = tmp
            i += 1; j -= 1
        return nums
```

## 33. 搜索旋转排序数组 <a id="rotation_search">📌</a>

> 整数数组 `nums` 按升序排列，数组中的值 **互不相同** 。
>
> 在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了 **旋转**，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标 **从 0 开始** 计数）。例如， `[0,1,2,4,5,6,7]` 在下标 `3` 处经旋转后可能变为 `[4,5,6,7,0,1,2]` 。
>
> 给你 **旋转后** 的数组 `nums` 和一个整数 `target` ，如果 `nums` 中存在这个目标值 `target` ，则返回它的下标，否则返回 `-1` 。
>
> 你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [4,5,6,7,0,1,2], target = 0
> 输出：4
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [4,5,6,7,0,1,2], target = 3
> 输出：-1
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [1], target = 0
> 输出：-1
> ```
>
>  
>
> **提示：**
>
> - `1 <= nums.length <= 5000`
> - `-104 <= nums[i] <= 104`
> - `nums` 中的每个值都 **独一无二**
> - 题目数据保证 `nums` 在预先未知的某个下标上进行了旋转
> - `-104 <= target <= 104`

accept答案：

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        '''
        本题的难点应当在于复杂度的规定，如果直接查找，复杂度应当是O(n)
        复杂度O(log n)代表算法的查找长度是递减的。
        思路：
        1. 二分法查找，主要分为以下几种情况
        - mid = target: 直接输出
        - nums[left] < nums[mid]: 前半段是有序数组，后半段是无序数组
            - nums[left] <= target < nums[mid]: right = mid - 1
            - else: left = mid + 1
        - else: 前半段是无序数组，后半段是有序的
            - nums[mid] < target <= nums[right]: left = mid + 1
            - else: right = mid - 1
        '''
        if not nums:
            return -1
        l, r = 0, len(nums) - 1
        while l <= r:
            mid = (l + r) // 2
            if nums[mid] == target:
                return mid
            if nums[0] <= nums[mid]:
                # 0 - mid 是有序数组
                if nums[0] <= target < nums[mid]:
                    # target在前半段
                    r = mid - 1
                else:
                    # target在后半段
                    l = mid + 1
            else:
                # 0 - mid中包含旋转点，mid - len是有序的
                if nums[mid] < target <= nums[len(nums) - 1]:
                    # target在后半段
                    l = mid + 1
                else:
                    # targe在前半段
                    r = mid - 1
        return -1
```

## 1710. 卡车上的最大单元数

> 请你将一些箱子装在 **一辆卡车** 上。给你一个二维数组 `boxTypes` ，其中 `boxTypes[i] = [numberOfBoxesi, numberOfUnitsPerBoxi]` ：
>
> - `numberOfBoxesi` 是类型 `i` 的箱子的数量。
> - `numberOfUnitsPerBoxi` 是类型 `i` 每个箱子可以装载的单元数量。
>
> 整数 `truckSize` 表示卡车上可以装载 **箱子** 的 **最大数量** 。只要箱子数量不超过 `truckSize` ，你就可以选择任意箱子装到卡车上。
>
> 返回卡车可以装载 **单元** 的 **最大** 总数*。*
>
>  
>
> **示例 1：**
>
> ```
> 输入：boxTypes = [[1,3],[2,2],[3,1]], truckSize = 4
> 输出：8
> 解释：箱子的情况如下：
> - 1 个第一类的箱子，里面含 3 个单元。
> - 2 个第二类的箱子，每个里面含 2 个单元。
> - 3 个第三类的箱子，每个里面含 1 个单元。
> 可以选择第一类和第二类的所有箱子，以及第三类的一个箱子。
> 单元总数 = (1 * 3) + (2 * 2) + (1 * 1) = 8
> ```
>
> **示例 2：**
>
> ```
> 输入：boxTypes = [[5,10],[2,5],[4,7],[3,9]], truckSize = 10
> 输出：91
> ```
>
>  
>
> **提示：**
>
> - `1 <= boxTypes.length <= 1000`
> - `1 <= numberOfBoxesi, numberOfUnitsPerBoxi <= 1000`
> - `1 <= truckSize <= 106`

accept答案：

```python
class Solution:
    def maximumUnits(self, boxTypes: List[List[int]], truckSize: int) -> int:
        '''
        思路：从最大容量的箱子入手，容量从大到小放即可
        '''
        d = {} # key: 容量, value: number of box
        for idx in range(len(boxTypes)):
            item = boxTypes[idx]
            if d.get(item[1]) is None:
                d[item[1]] = 0
            d[item[1]] += item[0]
        keys = list(d.keys())
        keys.sort()
        keys = keys[::-1]

        re, i = 0, 0  
        summ = 0 # 一共放了多少了
        while summ <= truckSize and i < len(keys):
            # 放入所有第i种箱子
            summ += d[keys[i]]
            re += keys[i] * d[keys[i]] # 目前的总数
            i += 1
        # break出来的时候第i-1种就放不下了，退回所有i-1容量的箱子
        summ -= d[keys[i-1]]
        re -= keys[i-1] * d[keys[i-1]]
        if d[keys[i-1]] > truckSize - summ: # i-1类型的小于可用容量
            re += keys[i-1] * (truckSize - summ)
        else: # 没有可用的箱子了
            re += keys[i-1] * d[keys[i-1]]
        return re
```

## 34. 在排序数组中查找元素的第一个和最后一个位置 <a id="search_first_and_last">📌</a>

> 给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。
>
> 如果数组中不存在目标值 `target`，返回 `[-1, -1]`。
>
> 你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [5,7,7,8,8,10], target = 8
> 输出：[3,4]
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [5,7,7,8,8,10], target = 6
> 输出：[-1,-1]
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [], target = 0
> 输出：[-1,-1]
> ```
>
>  
>
> **提示：**
>
> - `0 <= nums.length <= 105`
> - `-109 <= nums[i] <= 109`
> - `nums` 是一个非递减数组
> - `-109 <= target <= 109`

```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        '''
        O(log n): 二分法
        思路：先找到一个target的位置，然后向两边延展
        '''
        if len(nums) == 0: return [-1, -1]
        left, right = 0, len(nums)-1
        mid = (left + right) // 2
        while left <= right and right < len(nums) and left >= 0:
            mid = (left + right) // 2
            if nums[mid] == target:
                break
            elif nums[left] <= target < nums[mid]:
                right = mid - 1
            elif nums[mid] < target <= nums[right]: 
                left = mid + 1
            else:
                return [-1, -1]
        # target 在mid位置
        l_t, r_t, left, right = mid, mid, mid, mid
        while 0 <= l_t-1 <= mid:
            l_t -= 1
            if nums[l_t] == target:
                left = l_t
        while len(nums)> r_t+1 >= mid:
            r_t += 1
            if nums[r_t] == target:
                right = r_t
        return [left, right]
```

## 775.全局倒置与局部倒置 <a name="two_reverse">📌</a>

> 给你一个长度为 `n` 的整数数组 `nums` ，表示由范围 `[0, n - 1]` 内所有整数组成的一个排列。
>
> **全局倒置** 的数目等于满足下述条件不同下标对 `(i, j)` 的数目：
>
> - `0 <= i < j < n`
> - `nums[i] > nums[j]`
>
> **局部倒置** 的数目等于满足下述条件的下标 `i` 的数目：
>
> - `0 <= i < n - 1`
> - `nums[i] > nums[i + 1]`
>
> 当数组 `nums` 中 **全局倒置** 的数量等于 **局部倒置** 的数量时，返回 `true` ；否则，返回 `false` 。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [1,0,2]
> 输出：true
> 解释：有 1 个全局倒置，和 1 个局部倒置。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [1,2,0]
> 输出：false
> 解释：有 2 个全局倒置，和 1 个局部倒置。
> ```
>
>  
>
> **提示：**
>
> - `n == nums.length`
> - `1 <= n <= 10^5`
> - `0 <= nums[i] < n`
> - `nums` 中的所有整数 **互不相同**
> - `nums` 是范围 `[0, n - 1]` 内所有数字组成的一个排列

注意，暴力解决该问题会time out

暴力解决：树状数组

```python
class Solution:
    def isIdealPermutation(self, nums: List[int]) -> bool:
        '''
        思路：
        - 全局倒置：对任意两个下标i<j满足nums[i]>nums[j]
        - 局部倒置：对于两个相邻的下标i i+1，满足nums[i]>nums[i+1]
        其实局部倒置就是一种特殊的全局倒置，其中j = i + 1
        因此当全局倒置的数量等于局部倒置的数量时，有
        - 对于任意一个i，若他满足局部倒置，则不满足全局倒置
        - 有一个前提，对于下标i，他的全局和局部倒置对只能存在于i之前的数组中，所以我们要遍历i位置之前的所有位置来做上述判断
        - 或者，对下标i，遍历所有之后的位置来做判断
        【错误的方法，只检查满足当nums[i]>nums[i+1]，nums[i+1]之后是否还有比nums[i]小的】
        【这使得有一些全局倒置被漏检，倒置出错】
        '''
        lo, go = 0, 0
        for i in range(len(nums)-1):
            lo, go = 0, 0
            if nums[i] > nums[i+1]:
                lo += 1
                go += 1
            # 理想：i+2及其之后都大于nums[i]，即
            if i+2 < len(nums):
                if nums[i] < min(nums[i+2:]):
                    pass
                else:
                    return False
            
        if lo == go:
            return True
        else:
            return False
```

数学解法

```python
class Solution:
    def isIdealPermutation(self, nums: List[int]) -> bool:
        '''
        数学解法：
        满足题目要求的逆序对，需要满足nums[i]>nums[i+1]，且对于任意j>i+1都有nums[i]<nums[j]
        结合题目“nums中是从0:n-1的所有数字的排列”，因此
        【从数据生成的角度上】
        【满足要求的数组，必须是从正序排列的数组[0, 1, ..., n-1]中调换任意相邻两个位置的元素所成的】
        从数据生成的角度上：检查数字现在的位置和调换后的位置之间的差
        '''
        for i in range(len(nums)):
            if abs(nums[i] - i) > 1:
                return False
        return True

```

## 56. 合并区间<a name="combine_intervals">📌</a>

> 以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。
>
>  
>
> **示例 1：**
>
> ```
> 输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
> 输出：[[1,6],[8,10],[15,18]]
> 解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
> ```
>
> **示例 2：**
>
> ```
> 输入：intervals = [[1,4],[4,5]]
> 输出：[[1,5]]
> 解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
> ```
>
>  
>
> **提示：**
>
> - `1 <= intervals.length <= 10^4`
> - `intervals[i].length == 2`
> - `0 <= starti <= endi <= 10^4`

accept代码：

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        '''
        思路：看看能不能用O(n)的滑动窗口解决
        1. 对所有intervals中的区间按start的大小顺序排列
        2. 用滑动的双指针遍历所有区间
        '''
        starts = [s[0] for s in intervals]
        v2i_dict = {} # key: start, value: list of index
        for i in range(len(starts)):
            if v2i_dict.get(starts[i]) is None:
                v2i_dict[starts[i]] = []
            v2i_dict[starts[i]].append(i)
        all_start = list(v2i_dict.keys())
        all_start.sort()
        # 合并所有从同一start开始的区间
        result = [] # 按照start的大小顺序排列的intervals，start均不相同
        for start in all_start:
            # 所有从start开始的区间，其对应的索引为v2i_dict[start]
            ends = [intervals[idx][1] for idx in v2i_dict[start]]
            end = max(ends)
            result.append([start, end])
        # 双指针遍历result
        re = [result[0]]
        for idx in range(1, len(result)):
            if result[idx][0] > re[-1][1]: 
                # 如果下个区间的开始与上个区间的结束不相邻，注册新的区间
                re.append(result[idx])
            else:
                # 若下个区间的开始在上一个区间结束的之后一位，或者干脆等于或者小于上一个区间的结束，则两个区间合并
                re[-1][1] = max([re[-1][1], result[idx][1]])
        return re
```

## 75. 颜色分类

> 给定一个包含红色、白色和蓝色、共 `n` 个元素的数组 `nums` ，**[原地](https://baike.baidu.com/item/原地算法)**对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
>
> 我们使用整数 `0`、 `1` 和 `2` 分别表示红色、白色和蓝色。
>
> 
>
> 必须在不使用库的sort函数的情况下解决这个问题。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [2,0,2,1,1,0]
> 输出：[0,0,1,1,2,2]
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [2,0,1]
> 输出：[0,1,2]
> ```
>
>  
>
> **提示：**
>
> - `n == nums.length`
> - `1 <= n <= 300`
> - `nums[i]` 为 `0`、`1` 或 `2`

accept 答案

```python
:class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        '''
        思路：指针
        先给第一个位置为min(nums)
        i位置的时候，如果min(nums[i+1]) <= nums[i], 交换二者，指针移动
        '''
        for i in range(len(nums)-1):
            if min(nums[i+1:]) <= nums[i]:
                min_idx = nums[i+1:].index(min(nums[i+1:])) + 1 + i
                tmp = nums[i]
                nums[i] = nums[min_idx]
                nums[min_idx] = tmp
```

## 28. 最长连续序列 <a name="longest_con_array">📌</a>

> 给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
>
> 请你设计并实现时间复杂度为 `O(n)` 的算法解决此问题。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [100,4,200,1,3,2]
> 输出：4
> 解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [0,3,7,2,5,8,4,6,0,1]
> 输出：9
> ```
>
>  
>
> **提示：**
>
> - `0 <= nums.length <= 105`
> - `-109 <= nums[i] <= 109`

辅助下面的例子做理解：https://leetcode.cn/problems/longest-consecutive-sequence/solutions/3956/dong-tai-gui-hua-python-ti-jie-by-jalan/

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20221201100522811.png" alt="image-20221201100522811" style="zoom:50%;" />

accept答案：

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        '''
        思路：
        这个题很巧思，这里归纳一下，首先是题意。
        题意是想遍历整个数组，查看连续的数组有多长
        
        设立一个dict，key为一个nums中的数字，value为key参与的序列的最大长度
        每进来一个数字，如果这个数字是第一次出现
        1. 首先检查这个数字的出现，会不会使得已有的数字所在序列长度发生变化，如果有，则对其做更改
        2. 如果这个数字已经出现过，那就不用计算了
        具体的举例理解更清楚一些
        '''
        d = {}
        maxx = 0
        for num in nums:
            if d.get(num) is None:
                # 检查前后位置是否有已有的连续数列
                left = d.get(num-1, 0) # 没有这个数列，代表num-1位置的连续数列长度为0
                right = d.get(num+1, 0)
                # 
                length = left + right + 1 # num出现之后，num参与的序列长度，更新，如果左右邻居都没出现，则num所在的序列长度就是1
                d[num] = length
                d[num-left] = length
                d[num+right] = length
                maxx = max([maxx, length])
            else:
                pass
        return maxx
```

## 136. 只出现一次的数字 <a name="number_once">📌</a>

> 给你一个 **非空** 整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。
>
> 你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。
>
>  
>
> **示例 1 ：**
>
> ```
> 输入：nums = [2,2,1]
> 输出：1
> ```
>
> **示例 2 ：**
>
> ```
> 输入：nums = [4,1,2,1,2]
> 输出：4
> ```
>
> **示例 3 ：**
>
> ```
> 输入：nums = [1]
> 输出：1
> ```
>
>  
>
> **提示：**
>
> - `1 <= nums.length <= 3 * 104`
> - `-3 * 104 <= nums[i] <= 3 * 104`
> - 除了某个元素只出现一次以外，其余每个元素均出现两次。

这题我操牛逼

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        '''
        思路：线性时间复杂度好说，一次遍历即可，常量额外空间可以理解为，缓存区的大小是不变的
        '''
        # d = {} # 
        # for num in nums:
        #     if d.get(num) is None:
        #         # 第一次遍历到
        #         d[num] = 1
        #     elif d.get(num) == 1:
        #         # 第二次遍历到
        #         del(d[num])
        # keys = list(d.keys())
        # return keys[0]
        '''
        异或：异或满足交换律，即a^b^a = a^a^b = b
        '''
        ans = nums[0]
        for i in range(1, len(nums)):
            ans = ans ^ nums[i]
        return ans
```

## 79. 单词搜索 <a name="word_search">📌</a>

> 给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。
>
> 单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。
>
>  
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)
>
> ```
> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
> 输出：true
> ```
>
> **示例 2：**
>
> ![img](https://assets.leetcode.com/uploads/2020/11/04/word-1.jpg)
>
> ```
> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
> 输出：true
> ```
>
> **示例 3：**
>
> ![img](https://assets.leetcode.com/uploads/2020/10/15/word3.jpg)
>
> ```
> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
> 输出：false
> ```
>
>  
>
> **提示：**
>
> - `m == board.length`
> - `n = board[i].length`
> - `1 <= m, n <= 6`
> - `1 <= word.length <= 15`
> - `board` 和 `word` 仅由大小写英文字母组成

```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        '''
        思路：回溯
        写一个回溯check(i,j,k)表示从i,j位置是否能找到字符串word[k:]
        流程如下：（注意每次判断时需要检查某位置是否被遍历过）
        1. if board[i,j] != word[k] return False
        2. if board[i,j] == word[k]
            check[i,j+1,k+1]
        再加上一些边界的条件检查
        '''
        m, n = len(board), len(board[0])
        is_check = set() # (i, j)
        def check(i, j, k):
            if board[i][j] == word[k]:
                if k == len(word) - 1:
                    return True
                is_check.add((i, j))
                res = False
                if 0 <= i+1 < m and (i+1, j) not in is_check:
                    res = check(i+1, j, k+1)
                    if res is True: return res # 如果有一条路通了，就不用进行下面的判断了，否则会干扰结果
                if 0 <= i-1 < m and (i-1, j) not in is_check:
                    res = check(i-1, j, k+1)
                    if res is True: return res
                if 0 <= j-1 < n and (i, j-1) not in is_check:
                    res = check(i, j-1, k+1)
                    if res is True: return res
                if 0 <= j+1 < n and (i, j+1) not in is_check:
                    res = check(i, j+1, k+1)
                    if res is True: return res
                # 如果在任意一次回溯的时候return了false，则需要将对应的路径推出
                is_check.remove((i, j))
                # 少了这句：不论对于任何情况，都要给一个结果
                return res
            else:
                return False
        # 从任意一个与word[0]相同的位置开始查找
        pos = []
        for i in range(m):
            for j in range(n):
                if board[i][j] == word[0]:
                    pos.append((i, j))
        if len(pos) == 0:
            return False
        else:
            for p in pos:
                res = check(p[0], p[1], 0)
                if res is True:
                    return True
            return False
```

### 1759. 统计同构子字符串的数目

> 给你一个字符串 `s` ，返回 `s` 中 **同构子字符串** 的数目。由于答案可能很大，只需返回对 `109 + 7` **取余** 后的结果。
>
> **同构字符串** 的定义为：如果一个字符串中的所有字符都相同，那么该字符串就是同构字符串。
>
> **子字符串** 是字符串中的一个连续字符序列。
>
> **示例 1：**
>
> ```
> 输入：s = "abbcccaa"
> 输出：13
> 解释：同构子字符串如下所列：
> "a"   出现 3 次。
> "aa"  出现 1 次。
> "b"   出现 2 次。
> "bb"  出现 1 次。
> "c"   出现 3 次。
> "cc"  出现 2 次。
> "ccc" 出现 1 次。
> 3 + 1 + 2 + 1 + 3 + 2 + 1 = 13
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "xy"
> 输出：2
> 解释：同构子字符串是 "x" 和 "y" 。
> ```
>
> **示例 3：**
>
> ```
> 输入：s = "zzzzz"
> 输出：15
> ```
>
> **提示：**
>
> - `1 <= s.length <= 10^5`
> - `s` 由小写字符串组成

```python
class Solution:
    def countHomogenous(self, s: str) -> int:
        '''
        直接使用数学解法：
        其实示例中有点误导人，以“a”为例，“a”出现的三次构成如下：
        - index=0时的‘a’：出现一次
        - “aa”子序列：出现两次
        所以对于对于一个组中字符串的任意子字符串都为「同构子字符串」，而一个长度为 m 的字符串的子字符串的数目为 
                                frac{(m+1)m}{2}
        解读：
            (首项+末项)*项数/2
            长度为1的子序列出现m次，长度为2的子序列出现m-1次，……，长度为m的子序列出现1次
        '''
        summ = 0
        for k, v in groupby(s): # groupby这个函数找不到参考，试试原始的做法
            l = len(list(v))
            summ += int(((1+l)*l)/2)
        return summ % (10**9+7)
      
        ############ 原始解法 ###########
        summ = 0
        comp = s[0]
        l = 0 # 由comp构成的连续字符串的长度
        for letter in s:
            if letter == comp:
                l += 1
            else:
                summ += int(((1+l)*l)/2)
                # 更新comp
                comp = letter
                l = 1
        summ += int(((1+l)*l)/2)
        return summ % (10**9+7)
```

