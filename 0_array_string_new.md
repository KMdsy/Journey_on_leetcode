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

## 1807. 替换字符串中的括号内容

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

