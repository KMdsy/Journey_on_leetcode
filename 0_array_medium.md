---
title: 数组 Array (medium)
date: 2022-04-14 19:58:00
updated: 2022-04-29 22:32:00
tag:
- leetcode
- array
---

## 本章重点

1. 滑动窗口法（碰到需要双重遍历的题目，应当首先想到该方法）：双指针+hashmap（<a href="#longest_substr_no_repeat">查找最长不重复子串问题</a>）、双指针（[盛最多水的问题](#largest_area)）、双指针+排序（[三数之和问题](#three_sum)、[最接近的三数之和](#closest_target)、[四数之和问题](#four_sum)）、
2. 动态规划法，详见<a href="#longest_palindromic_substr">最长回文子串问题</a>
3. 回溯（backtrack）：[电话号码的所有组合](#comb_phonenumber)

## 本章题目思路记忆要点

1. **两数相加**：储存在倒序链表中的两个数字（个位在链表最外侧），求和时可以直接考虑边循环边直接相加<u>每一位</u>，保存进位数即可。
2. **无重复的最长子串**：基于<u>双指针</u>的滑动窗口法，涉及<u>“无重复”</u>均直接采用`set`或者`dict`实现的`hashmap`。
3. **Z字形变换**：注意答案要求，仅考虑每一行存储的字母顺序即可，无需精确到具体的坐标
4. **整数转罗马字**：数字转罗马字的思路为，对数字`num`寻找不超过该数的符号表示，然后`num`减去可被符号表示的部分，直到`num`为0
5. **整数翻转**：假设环境中不允许存储64位数字，那我们需要在乘10运算之前，将output与`2^31 // 10`做比较以保证不超过该数。
6. **删除链表的倒数第N个节点**：可以用双指针法，注意哑节点（指向头部节点的虚拟节点）的使用，可以避免头节点的判断。
7. **生成括号**：生成包含`n`个括号的问题`gp(n)`，其基本形式为`(a)b`，`a`, `b`分别代表一个有效的括号序列，`a`, `b`可以为空。对偶问题——[验证括号有效性](./0_array_easy.md#valid_parentheses)

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
> - ``a`、`b`、`c `和` d `互不相同
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



