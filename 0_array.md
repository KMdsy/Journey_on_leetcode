# 数组 Array



## 1. 两数之和

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411151334618.png" alt="image-20220411151334618" style="zoom:50%;" />

**原accept答案**：简单优化的暴力破解。

**优化要点**：所有看似能够暴力破解的问题，都不要尝试用暴力破解。本题的要点在于“将问题转为‘查询’问题”，即对于`nums[j]`，查询`target - nums[j]`所在的位置。查询可使用<u>哈希表</u>的思想。

哈希表也叫散列表，它是根据关键码值而直接进行访问的数据结构，它把一个键直接映射到某个位置而进行访问和记录，这个映射函数就叫做哈希函数，而这个存放记录的数组就像叫做哈希表。哈希表可以通过键直接访问到所存储的值，其效率和数组直接访问数据相当，时间复杂度是O(1)，并且在插入和删除操作的时候，时间复杂度仍然是O(1),与链表的效率相当，因此，在进行大量数据的访问和插入删除操作的时候，哈希表是一个很好的选择。

**解题思想**：将`nums`转化为字典（字典是一种哈希表的形式），利用哈希表查询时间复杂度为$O(1)$的性质，查询`target - nums[j]`所在的位置。

**优化后的答案**：

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num2idx_dict = {}
        output = None
        for idx, value in enumerate(nums):
            other_idx = num2idx_dict.get(target - value)
            if other_idx is not None:
                return [min([idx, other_idx]), max([idx, other_idx])]
            num2idx_dict[value] = idx # 不能不加判断的直接送入dict，会导致key冲突
        return output

```



## 2. 回文数

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411160948137.png" alt="image-20220411160948137" style="zoom:50%;" />

**原accept答案**：

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0:
            return False
        x_str = str(x)

        if len(x_str) % 2 == 0:
            # 0,1,2,3 -> 1
            med = len(x_str) // 2 - 1
        else:
            # 0,1,2,3,4 -> 2
            med = len(x_str) // 2
        for idx in range(med+1):
            if x_str[idx] != x_str[-(idx+1)]:
                return False
        return True
```

**优化思路**：可以考虑不将数字字符串化的做法，<u>截取数字的后半段并做反转，来比较是否与前半段相等</u>。需要注意的要点包括

- 注意边界值的处理：负数均不为回文数，以0结尾的数字，只有0本身回文
- 如何判断是否正好取了一半的数字：当原数字<u>小于或者等于</u>截取反转的数字的时候，此时正好取了一半数字。
- 对于长度为奇数的数字，反转数字中将包括最中间的值，需要通过判断`origin_num == revert_num // 10`来去掉位于对称轴的数字以获得正确输出。

**进阶答案**：

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        origin_num = x
        revert_num = 0
        if origin_num < 0:
            return False
        elif origin_num % 10 == 0:
            if origin_num == 0:
                return True
            else:
                return False
        while revert_num < origin_num:
            revert_num = revert_num * 10 + origin_num % 10
            origin_num = origin_num // 10
        if origin_num == revert_num or origin_num == revert_num // 10:
            return True
        return False
```



## 3. 罗马数字转整数

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411201635950.png" alt="image-20220411201635950" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411201655047.png" alt="image-20220411201655047" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411201710589.png" alt="image-20220411201710589" style="zoom:50%;" />

**题解要点**：该题目的要点在于分析罗马字的转换原理——“<u>依次成对比较</u>”，即对于字符`str[i]`，若`str[i]`代表的数字小于`str[i+1]`，则`str[i]`应当按负数处理；反之则按正数处理。注意边界条件的影响

**答案**：

```python
class Solution:
    def romanToInt(self, s: str) -> int:
        roman_dict = {'I':1, 'V':5, 'X':10, 'L':50, 'C':100, 'D':500, 'M':1000}
        roman_str = s
        output = 0
        for idx in range(len(roman_str)):
            if idx < len(roman_str) - 1:
                if roman_dict[roman_str[idx]] < roman_dict[roman_str[idx+1]]:
                    output -= roman_dict[roman_str[idx]]
                else:
                    output += roman_dict[roman_str[idx]]
            else:
                output += roman_dict[roman_str[idx]]
        return output
```



## 4. 最长公共前缀

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411203558873.png" alt="image-20220411203558873" style="zoom:50%;" />

**原accept答案**：

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        input_list = strs
        min_len = min(len(item) for item in input_list)
        output = []
        for letter_idx in range(min_len):
            tmp_re = True
            for idx in range(1, len(input_list)):
                if input_list[idx][letter_idx] != input_list[idx-1][letter_idx]:
                    tmp_re = False
                    break
            if tmp_re:
                output.append(input_list[0][letter_idx])
            else:
                break
        return ''.join(output)
```

**进阶**：还可以用分治（递归）和二分法查找，但感觉在这个题中没必要，先省略。



## 5. 有效的括号

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411213537532.png" alt="image-20220411213537532" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220411213557958.png" alt="image-20220411213557958" style="zoom:50%;" />

**解题要点**：需要理清以下思路，<u>遇到左括号一律进栈，遇到右括号一律与栈尾进行匹配检查</u>。注意以下边界条件：

- 输入字符串的长度为奇数的时候，可以直接返回`False`
- 匹配左右括号的时候，栈不能为空
- 算法结束要检查栈是否为空
- 左括号不可以进栈

**原accept答案**：

```python
class Solution:
    def isValid(self, s: str) -> bool:
        input_str = s
        coding_book = {'(': -1, ')': 1, '{':-10, '}':10, '[':-100, ']':100}
        tmp_list = []
        output = True

        if len(input_str) % 2 != 0:
            return False

        for idx, value in enumerate(input_str):
            if coding_book[value] < 0:
                tmp_list.append(coding_book[value])
            elif len(tmp_list) > 0 and coding_book[value] == - tmp_list[-1] and coding_book[value] > tmp_list[-1]:
                tmp_list.pop()
            else:
                output = False
                break
        if len(tmp_list) > 0:
            return False
        return output


```

**优化思路**：参考官方答案，可以设置左括号为`key`，右括号为`value`，增加了代码的可读性。

```python
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) % 2 == 1:
            return False
        
        pairs = {
            ")": "(",
            "]": "[",
            "}": "{",
        }
        stack = list()
        for ch in s:
            if ch in pairs:
                if not stack or stack[-1] != pairs[ch]:
                    return False
                stack.pop()
            else:
                stack.append(ch)
        
        return not stack
```

