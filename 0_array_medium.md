---
title: 数组 Array (medium)
date: 2022-04-14 19:58:00
updated: 2022-04-27 21:17:00
tag:
- leetcode
- array
---

## 本章重点

1. 滑动窗口法（碰到需要双重遍历的题目，应当首先想到该方法）：双指针+hashmap（<a href="#longest_substr_no_repeat">查找最长不重复子串问题</a>）、双指针（[盛最多水的问题](#largest_area)）
2. 动态规划法，详见<a href="#longest_palindromic_substr">最长回文子串问题</a>

## 本章题目思路记忆要点

1. **两数相加**：储存在倒序链表中的两个数字（个位在链表最外侧），求和时可以直接考虑边循环边直接相加<u>每一位</u>，保存进位数即可。
2. **无重复的最长子串**：基于<u>双指针</u>的滑动窗口法，涉及<u>“无重复”</u>均直接采用`set`或者`dict`实现的`hashmap`。
3. **Z字形变换**：注意答案要求，仅考虑每一行存储的字母顺序即可，无需精确到具体的坐标
4. **整数转罗马字**：数字转罗马字的思路为，对数字`num`寻找不超过该数的符号表示，然后`num`减去可被符号表示的部分，直到`num`为0

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

