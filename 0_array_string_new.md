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

1. 

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

