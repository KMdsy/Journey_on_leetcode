---
title: 数组Array (easy)
date: 2022-04-11 10:12:00
updated: 2022-05-18 21:02:00
tag:
- leetcode
- array
---



## 本章重点

1. 字符串匹配，Sunday算法（<a href="#strstr">寻找子串问题</a>）
2. 借助字典实现的hashmap，实现$O(1)$的查询（<a href="#twosum">两数之和问题</a>）
3. <u>原地</u>删除列表中的元素，不能使用python自带的pop或者del，利用<u>双指针</u>做元素替换/拷贝（<a href="#deleterep">删除重复元素</a>、<a href="#deletenum">删除指定数字</a>）
4. 深度优先搜索、广度优先搜索（[相同的树](#same_tree)）

## 本章题目思路记忆要点

1. **回文数**：1⃣️ 可以考虑不将数字字符串化的做法，<u>截取数字的后半段并做反转，来比较是否与前半段相等</u>。2⃣️ 如何判断是否正好取了一半的数字：当原数字<u>小于或者等于</u>截取反转的数字的时候，此时正好取了一半数字。
2. **罗马数转整数**：罗马字的转换原理——“<u>依次成对比较</u>”，即对于字符`str[i]`，若`str[i]`代表的数字小于`str[i+1]`，则`str[i]`应当按负数处理；反之则按正数处理。
3. **最长公共前缀**：依次比较所有字符串中的第一位至最后一位，直至从某位开始，内容不相等。
4. **有效的括号**：1⃣️ 遇到左括号一律进栈，遇到右括号一律与栈尾进行匹配检查。2⃣️ 算法结束时需要检查栈是否为空。
5. **合并两个有序列表**：🌟<u>递归思想</u>：对于两个非空的链表`list1`，`list2`，其有序排列问题，可以分解为“<u>两个链表中具有较小值的表头，链接到另一个**有序链表**</u>”

## 题目

### 1. 两数之和 <a id="twosum">📌</a>

> 给定一个整数数组` nums `和一个整数目标值 `target`，请你在该数组中找出和为目标值 `target  `的那两个整数，并返回它们的数组下标。
>
> 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。你可以按任意顺序返回答案。
>
> 
>
> **示例 1**：
>
> ```
> 输入：nums = [2,7,11,15], target = 9
> 输出：[0,1]
> 解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [3,2,4], target = 6
> 输出：[1,2]
> ```
>
> **示例 3**：
>
> ```
> 输入：nums = [3,3], target = 6
> 输出：[0,1]
> ```
>
> 
>
> **提示**：
>
> - `2 <= nums.length <= 104`
> - `-109 <= nums[i] <= 109`
> - ``-109 <= target <= 109`
> 
> 只会存在一个有效答案
>进阶：你可以想出一个时间复杂度小于$O(n^2)$的算法吗？



**原accept答案**：简单优化的暴力破解。

**优化要点**：所有看似能够暴力破解的问题，都不要尝试用暴力破解。本题的要点在于“将问题转为‘查询’问题”，即对于`nums[j]`，查询`target - nums[j]`所在的位置。查询可使用<u>哈希表</u>的思想。

哈希表也叫散列表，它是根据关键码值而直接进行访问的数据结构，它把一个键直接映射到某个位置而进行访问和记录，这个映射函数就叫做哈希函数，而这个存放记录的数组就像叫做哈希表。哈希表可以通过键直接访问到所存储的值，其效率和数组直接访问数据相当，时间复杂度是$O(1)$，并且在插入和删除操作的时候，时间复杂度仍然是O(1),与链表的效率相当，因此，在进行大量数据的访问和插入删除操作的时候，哈希表是一个很好的选择。

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



### 2. 回文数

> 给你一个整数 `x` ，如果 `x` 是一个回文整数，返回 `true` ；否则，返回 `false` 。
>
> 回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。
>
> 例如，121 是回文，而 123 不是。
>
> **示例 1**：
>
> ```
> 输入：x = 121
> 输出：true
> ```
>
> **示例 2**：
>
> ```
> 输入：x = -121
> 输出：false
> 解释：从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
> ```
>
> **示例 3**：
>
> ```
> 输入：x = 10
> 输出：false
> 解释：从右向左读, 为 01 。因此它不是一个回文数。
> ```
>
> **提示**：
>
> - `-231 <= x <= 231 - 1`
> 
> **进阶**：你能不将整数转为字符串来解决这个问题吗？



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



### 3. 罗马数字转整数

> 罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。
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
> 
>
> 例如， 罗马数字 2 写做 II ，即为两个并列的 1 。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。
>
> 通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：
>
> - I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
> - X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
> - C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
>
> 给定一个罗马数字，将其转换成整数。
>
>  
>
> 示例 1:
>
> ```
> 输入: s = "III"
> 输出: 3
> ```
>
> 示例 2:
>
> ```
> 输入: s = "IV"
> 输出: 4
> ```
>
>
> 示例 3:
>
> ```
> 输入: s = "IX"
> 输出: 9
> ```
>
> 示例 4:
>
> ```
> 输入: s = "LVIII"
> 输出: 58
> 解释: L = 50, V= 5, III = 3.
> ```
>
>
> 示例 5:
>
> ```
> 输入: s = "MCMXCIV"
> 输出: 1994
> 解释: M = 1000, CM = 900, XC = 90, IV = 4.
> ```
>
> 提示：
>
> - `1 <= s.length <= 15`
> - `s `仅含字符` ('I', 'V', 'X', 'L', 'C', 'D', 'M')`
> 
> 
>题目数据保证 s 是一个有效的罗马数字，且表示整数在范围 [1, 3999] 内
>
> 题目所给测试用例皆符合罗马数字书写规则，不会出现跨位等情况。
>
> IL 和 IM 这样的例子并不符合题目要求，49 应该写作 XLIX，999 应该写作 CMXCIX 。
>
> 关于罗马数字的详尽书写规则，可以参考 [罗马数字 - Mathematics ](https://b2b.partcommunity.com/community/knowledge/zh_CN/detail/10753/罗马数字#knowledge_article) 。



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



### 4. 最长公共前缀

> 编写一个函数来查找字符串数组中的最长公共前缀。
>
> 如果不存在公共前缀，返回空字符串` ""`。
>
> **示例 1**：
>
> ```
> 输入：strs = ["flower","flow","flight"]
> 输出："fl"
> ```
>
> **示例 2**：
>
> ```
> 输入：strs = ["dog","racecar","car"]
> 输出：""
> 解释：输入不存在公共前缀。
> ```
>
> **提示**：
>
> - `1 <= strs.length <= 200`
> - `0 <= strs[i].length <= 200`
> - `strs[i] `仅由小写英文字母组成



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



### 5. 有效的括号<a id="valid_parentheses">📌</a>

> 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串` s `，判断字符串是否有效。
>
> 有效字符串需满足：
>
> 左括号必须用相同类型的右括号闭合。
> 左括号必须以正确的顺序闭合。
>
> **示例 1**：
>
> ```
> 输入：s = "()"
> 输出：true
> ```
>
> **示例 2**：
>
> ```
> 输入：s = "()[]{}"
> 输出：true
> ```
>
> **示例 3**：
>
> ```
> 输入：s = "(]"
> 输出：false
> ```
>
> **示例 4**：
>
> ```
> 输入：s = "([)]"
> 输出：false
> ```
>
> **示例 5**：
>
> ```
> 输入：s = "{[]}"
> 输出：true
> ```
>
> **提示**：
>
> - `1 <= s.length <= 104`
> - `s `仅由括号` '()[]{}' `组成
> 
> 



**解题要点**：需要理清以下思路，<u>遇到左括号一律进栈，遇到右括号一律与栈尾进行匹配检查</u>。注意以下边界条件：

- 输入字符串的长度为奇数的时候，可以直接返回`False`
- 匹配左右括号的时候，栈不能为空
- 算法结束要检查栈是否为空
- 右括号不可以进栈

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



### 6. 合并两个有序列表

> 将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 
>
> **示例 1：**
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220413161429202.png" alt="image-20220413161429202" width="70%" />
> </div>
>
> 
>
> ```
> 输入：l1 = [1,2,4], l2 = [1,3,4]
> 输出：[1,1,2,3,4,4]
> ```
>
> **示例 2：**
>
> ```
> 输入：l1 = [], l2 = []
> 输出：[]
> ```
>
> **示例 3**：
>
> ```
> 输入：l1 = [], l2 = [0]
> 输出：[0]
> ```
>
> **提示**：
>
> - 两个链表的节点数目范围是 `[0, 50]`
> - `-100 <= Node.val <= 100`
> - `l1` 和` l2 `均按 **非递减顺序** 排列



**原accept答案**：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        if list1 is None and list2 is None:
            return None
        elif list1 is None:
            return list2
        elif list2 is None:
            return list1

        output = ListNode(-200)
        head = output # 应该是传址
        while list1 and list2:
            if list1.val <= list2.val:
                output.next = ListNode(list1.val)
                list1 = list1.next
            elif list1.val > list2.val:
                output.next = ListNode(list2.val)
                list2 = list2.next
            output = output.next
        if list1 is None:
            rear = list2
        else:
            rear = list1
        output.next = rear
        return head.next
```

**解题要点**：本题出现了以前不常用的“**方法参数类型注释**”，且使用到了`typing`模块（需要学习一下）。本题没有难度，关键在于链表的构造，需要多加记忆。

**优化思路**：参考题解，本题可以采用递归。具体思路：不考虑边界情况，对于两个非空的链表`list1`，`list2`，其有序排列问题，可以分解为“<u>两个链表中具有较小值的表头，链接到另一个有序链表</u>”的问题，则问题分解为：

1. 找到具有较小值的表头，记为`head`
2. 将表头`head`链接到递归形成的有序链表中

**进阶答案**：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        if list1 is None and list2 is None:
            return None
        elif list1 is None:
            return list2
        elif list2 is None:
            return list1

        if list1.val <= list2.val:
            list1.next = self.mergeTwoLists(list1.next, list2)
            return list1
        else:
            list2.next = self.mergeTwoLists(list2.next, list1)
            return list2
```



### 7. 删除有序数组中的重复项 <a id="deleterep">📌</a>

> 给你一个 升序排列 的数组` nums` ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。
>
> 由于在某些语言中不能改变数组的长度，所以必须将结果放在数组`nums`的第一部分。更规范地说，如果在删除重复项之后有` k `个元素，那么 `nums `的前 `k `个元素应该保存最终结果。
>
> 将最终结果插入 nums 的前` k `个位置后返回` k `。
>
> 不要使用额外的空间，你必须在 原地 修改输入数组 并在使用$O(1)$额外空间的条件下完成。
>
> **判题标准:**
>
> 系统会用下面的代码来测试你的题解:
>
> ```java
> int[] nums = [...]; // 输入数组
> int[] expectedNums = [...]; // 长度正确的期望答案
> 
> int k = removeDuplicates(nums); // 调用
> 
> assert k == expectedNums.length;
> for (int i = 0; i < k; i++) {
>     assert nums[i] == expectedNums[i];
> }
> ```
>
> 如果所有断言都通过，那么您的题解将被 **通过**。
>
> **示例 1**：
>
> ```
> 输入：nums = [1,1,2]
> 输出：2, nums = [1,2,_]
> 解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [0,0,1,1,1,2,2,3,3,4]
> 输出：5, nums = [0,1,2,3,4]
> 解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。
> ```
>
> **提示**：
>
> - `0 <= nums.length <= 3 * 104`
> - `-104 <= nums[i] <= 104`
> - `nums `已按 升序 排列



**解题要点**：本题要点在于$O(1)$的额外空间用量，以及<u>原地删除</u>。

⚠️ 虽然在python中使用了`del(list[index])`或者`list.pop(index)`可以删除`list`中的元素，但由于OJ系统的适配问题，这种方法并不能accept。⚠️

因此需要借助“<u>内存覆盖</u>”来删除原值。<u>双指针</u>的思路：

- 设置快指针`fast`与慢指针`slow`
- `slow`指下一个不重复的数字需要填入的位置，`slow`指针之前的序列均为有序不重复序列
- `fast`用于遍历列表中的每个元素



**答案**：

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        if len(nums) <= 1:
            return len(nums)
        slow, fast = 1,1
        while fast < len(nums):
            if nums[fast] == nums[slow-1]:
                pass
            else:
                nums[slow] = nums[fast]
                slow += 1
            fast += 1
        return len(nums[0:slow])
```



### 8. 移除元素 <a id="deletenum">📌</a>

> 给你一个数组` nums `和一个值` val`，你需要 原地 移除所有数值等于` val `的元素，并返回移除后数组的新长度。
>
> 不要使用额外的数组空间，你必须仅使用$ O(1) $额外空间并 原地 修改输入数组。
>
> 元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。
>
>  
>
> **说明**:
>
> 为什么返回数值是整数，但输出的答案是数组呢?
>
> 请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。
>
> 你可以想象内部操作如下:
>
> ```java
> // nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
> int len = removeElement(nums, val);
> 
> // 在函数里修改输入数组对于调用者是可见的。
> // 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
> for (int i = 0; i < len; i++) {
>     print(nums[i]);
> }
> ```
>
> **示例 1**：
>
> ```
> 输入：nums = [3,2,2,3], val = 3
> 输出：2, nums = [2,2]
> 解释：函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。你不需要考虑数组中超出新长度后面的元素。例如，函数返回的新长度为 2 ，而 nums = [2,2,3,3] 或 nums = [2,2,0,0]，也会被视作正确答案。
> ```
>
> **示例 2**：
>
> ```
> 输入：nums = [0,1,2,2,3,0,4,2], val = 2
> 输出：5, nums = [0,1,4,0,3]
> 解释：函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。注意这五个元素可为任意顺序。你不需要考虑数组中超出新长度后面的元素。
> ```
>
> **提示**：
>
> - `0 <= nums.length <= 100`
> - `0 <= nums[i] <= 50`
> - `0 <= val <= 100`



**原accept答案**：

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:        
        slow, fast = 0, 0
        while fast < len(nums):
            if nums[fast] == val:
                pass
            else:
                nums[slow] = nums[fast]
                slow += 1
            fast += 1
        return len(nums[0:slow])
```



**题解要点**：本题要点还是“<u>双指针</u>”。

**优化思路**：参考题解，<u>双指针</u>方法可以优化，设置左右指针：

- 左指针：指向正在比较的位置，如果该位置的值等于`val`，则将右指针出的值拷贝过去。左指针之前均不存在要删除的值。
- 右指针：指向可以拷贝的位置，拷贝事件每发生一次，指针移动一次
- 左右指针在中间相遇时，循环结束。

**优化答案**：

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:        
        left, right = 0, len(nums) - 1
        while left <= right:
            if nums[left] == val:
                nums[left] = nums[right]
                right -= 1
            else:
                left += 1
        return len(nums[0:left])
```



### 9. 实现 strStr() 函数  <a id="strstr"> 📌</a>

> 给你两个字符串` haystack `和` needle `，请你在` haystack `字符串中找出` needle `字符串出现的第一个位置（下标从` 0 `开始）。如果不存在，则返回`  -1 `。
>
> **说明**：
>
> 当` needle `是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
>
> 对于本题而言，当` needle `是空字符串时我们应当返回` 0 `。这与 C 语言的` strstr() `以及 Java 的` indexOf() `定义相符。
>
>  
>
> **示例 1**：
>
> ```
> 输入：haystack = "hello", needle = "ll"
> 输出：2
> ```
>
> **示例 2**：
>
> ```
> 输入：haystack = "aaaaa", needle = "bba"
> 输出：-1
> ```
>
> **示例 3**：
>
> ```
> 输入：haystack = "", needle = ""
> 输出：0
> ```
>
> **提示**：
>
> - `1 <= haystack.length, needle.length <= 104`
> - `haystack `和` needle `仅由小写英文字符组成



**原accept代码（暴力）**：

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        pos_list = [] # 记录haystack中所有子串可能开始的位置
        str_len = len(needle)
        if str_len == 0:
            return 0
        for idx in range(len(haystack)):
            if haystack[idx] == needle[0]:
                pos_list.append(idx)
        for idx in pos_list:
            if haystack[idx:idx+str_len] == needle:
                return idx
        return -1
```

**优化思路（Sunday算法）**<a id="sunday">📌</a>：

Sunday算法比KMP算法好理解的多，先对这个算法做概述。先做如下定义：

- 目标字符串：`String`

- 模式串：`Pattern`

- 当前查询索引：`idx `（初始为 0）

- 待匹配字符串：`str_cut: String [ idx : idx + len(Pattern) ]`

Sunday定义了一个<u>偏移表</u>，用于移动匹配框。偏移表针对每个出现在`Pattern`中的字符定义：

- 针对`Pattern`中出现的字符`c`，`偏移表[c]`定义为：最右侧的`c`距离`Pattern`尾的距离`+ 1`

Sunday的流程如下：

1. 从目标串`String`中提取子串`str_cut`与`Pattern`进行匹配，若匹配则返回，不匹配则观察`str_cut`的下一位字符`c`是否出现在`Pattern`中
    - 若出现：当前查询索引移动`偏移表[c]`个位置
    - 若未出现：当前查询索引移动`len(Pattern) + 1`个位置
2. 循环，直到找到，或`idx + len(Pattern) > len(String)`

**优化答案（Sunday算法）**：

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        if needle == '':
            return 0
        # 建立偏移表
        table = {}
        for idx in range(len(needle)):
            letter = needle[-(idx+1)]
            if letter not in table.keys():
                table[letter] = idx + 1
        # 匹配
        search_idx = 0 # 查找起始点
        str_len = len(needle)
        while search_idx + str_len <= len(haystack):
            if needle == haystack[search_idx:search_idx+str_len]:
                return search_idx
            else:
                if search_idx + str_len < len(haystack):
                    if haystack[search_idx + str_len] in needle:
                        search_idx += table[haystack[search_idx+str_len]]
                    else:
                        search_idx += str_len + 1
                else:
                    return -1
        return -1
```



### 10. 搜素插入位置

> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
>
> 请必须使用时间复杂度为` O(log n) `的算法。 
>
> **示例 1**:
>
> ```
> 输入: nums = [1,3,5,6], target = 5
> 输出: 2
> ```
>
> **示例 2**:
>
> ```
> 输入: nums = [1,3,5,6], target = 2
> 输出: 1
> ```
>
> **示例 3**:
>
> ```
> 输入: nums = [1,3,5,6], target = 7
> 输出: 4
> ```
>
> **提示**:
>
> - `1 <= nums.length <= 104`
> - `-104 <= nums[i] <= 104`
> -  `nums `为 **无重复元素** 的 **升序** 排列数组
> - `-104 <= target <= 104`

**解题要点**：题目中的$O(\log n)$的时间复杂度要求，表明此题不可以顺序查找，因此考虑用递归写二分法查找。

**原accept代码**：

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        def search(input_list, value, last_idx):
            if len(input_list) == 1:
                if value > input_list[0]:
                    return last_idx + 1
                elif value <= input_list[0]:
                    return last_idx
            # input_list中第一个元素在原序列中的索引 
            start_idx = last_idx - len(input_list) + 1 
            idx = len(input_list) // 2
            # 本次被比较的数字在原序列中的索引 - 1
            middle_idx = start_idx + idx - 1
            if input_list[idx] == value :
                return start_idx + idx
            elif input_list[idx] < value:
                return search(input_list[idx:], value, last_idx)
            else:
                return search(input_list[:idx], value, middle_idx)
        result = search(nums, target, len(nums) - 1)
        return result
```

**简单版的二分法**：

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1
        while left <= right:
            middle = (right + left) // 2
            if nums[middle] == target:
                return middle
            elif nums[middle] < target:
                left = middle + 1
            else:
                right = middle - 1
        # 注意，由于这里是输入的是升序的列表，所以在没有搜索到目标值时
        # 返回比target较小的指针，即left
        return left
```

## 11. 相同的树<a id="same_tree">📌</a>

> 给你两棵二叉树的根节点` p `和` q `，编写一个函数来检验这两棵树是否相同。
>
> 如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。
>
>  
>
> **示例 1**：
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220518163702940.png" width="35%" />
> </div>
>
> ```
> 输入：p = [1,2,3], q = [1,2,3]
> 输出：true
> ```
>
> **示例 2**：
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220518163853023.png" width="25%" />
> </div>
>
> ```
> 输入：p = [1,2], q = [1,null,2]
> 输出：false
> ```
>
> **示例 3**：
>
> <div align="center">
>   <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20220518163946471.png" width="35%" />
> </div>
>
> ```
> 输入：p = [1,2,1], q = [1,1,2]
> 输出：false
> ```
>
> **提示**：
>
> - 两棵树上的节点数目都在范围 `[0, 100]` 内
> - `-10^4 <= Node.val <= 10^4`

**解题思路**：使用递归+深度遍历，即可解题。进阶的，可以考虑广度搜索。

**原accept代码**：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        # 深度优先搜索
        if p is None and q is None:
            return True
        else:
            if p is not None and q is not None:
                res0 = p.val == q.val
                res1 = self.isSameTree(p.left, q.left)
                res2 = self.isSameTree(p.right, q.right)
                return res0 and res1 and res2
            else:
                return False
```

**广度优先搜索**：

广度优先搜索的重点在于维护一个队列，该队列永远存储待判断的所有节点，并按照层级依次进队列。在判断节点是否具有相同结构前，节点从队列中推出。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        # 广度优先搜索
        if p is None and q is None:
            return True
        if p is None or q is None:
            return False
        if p.val != q.val: 
            return False
        # 该节点一致
        queue1 = collections.deque([p])   
        queue2 = collections.deque([q])
        while len(queue1) != 0 and len(queue2) != 0:
            node1 = queue1.pop()
            node2 = queue2.pop()
            if node1 is None and node2 is None:
                continue
            if node1 is None or node2 is None:
                return False
            if node1.val != node2.val:
                return False
            queue1.appendleft(node1.left)
            queue1.append(node1.right)
            queue2.appendleft(node2.left)
            queue2.append(node2.right)
        return True
```

