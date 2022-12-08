---
title: Dynamic Programming (动态规划) 
date: 2022-08-19 14:00:00
updated: 2022-12-08 23:36:00
tag:
- leetcode
- dynamic programming
---

## 算法思路

动态规划（Dynamic programming，DP），是一种在数学、管理科学、计算机科学、经济学和生物信息学中使用的，通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。动态规划常常适用于有重叠子问题和最优子结构性质的问题。

以上定义来自维基百科，看定义感觉还是有点抽象。简单来说，动态规划其实就是，给定一个问题，我们把它**拆成一个个子问题**，直到子问题可以直接解决。然后呢，**把子问题答案保存起来**，以减少重复计算。再**根据子问题答案反推**，得出原问题解的一种方法。

动态规划有几个典型特征，**最优子结构、状态转移方程、边界、重叠子问题**。在青蛙跳阶问题中：

- f(n-1)和f(n-2) 称为 f(n) 的最优子结构
- f(n)= f（n-1）+f（n-2）就称为状态转移方程
- f(1) = 1, f(2) = 2 就是边界啦
- 比如f(10)= f(9)+f(8),f(9) = f(8) + f(7) ,f(8)就是重叠子问题。



（引自[link](https://zhuanlan.zhihu.com/p/365698607)）



## 题目思路记忆要点

1. [跳跃游戏I/II](#jump_1)：不知道为什么放在这一章，采用的是贪心算法，在每个位置上先评估是否可以一次跳跃到终点，如果不能，则在可以跳跃的位置处分别评估第二跳可以到达的最远位置，来选择下一跳的位置。跳跃游戏II也是这个原理
1. [单词拆分](#split_words): 这个题还没搞懂，要反复记忆

## 题目

### 55. 跳跃游戏 <a name="jump_1">📌</a>

> 给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 判断你是否能够到达最后一个下标。
>
>  
>
> **示例 1：**
>
> ```
>输入：nums = [2,3,1,1,4]
> 输出：true
> 解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
> ```
> 
>    **示例 2：**
> 
>```
> 输入：nums = [3,2,1,0,4]
>输出：false
> 解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
> ```
> 
>  
>
>**提示：**
> 
>- `1 <= nums.length <= 3 * 104`
> - `0 <= nums[i] <= 105`

**解题思路**：贪婪算法——通过局部最优解得到全局最优解。

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        '''
        思路：在每个位置都判断自己是否能够到达最后一个位置
        如果不能，分别评估在所有能跳的地方，下一跳最远能到哪，然后选择第二跳能跳的最远的位置起跳
        '''
        step = 0 # 现在的位置
        end = len(nums) - 1
        while step < end:
            if step + nums[step] >= end:
                return True
            else:
                if nums[step] == 0: # 该位置无法起跳，False
                    return False
                maxx = -1 # 最远能跳多远
                next_jump = -1 # 选定的起跳位置
                for jump in range(1, nums[step]+1):
                    if jump + step >= len(nums):
                        continue
                    if jump + step + nums[jump + step] > maxx:
                        maxx = jump + step + nums[jump + step]
                        next_jump = jump
                # 至此，已经找到了下次能跳的最远的位置，跳jump次
                step += next_jump
        # 这里注意一个边界条件，step == end的时候，没有进循环，但已经到达最后一个坐标了
        return True
```

## 45. 跳跃游戏 II

> 给你一个非负整数数组 `nums` ，你最初位于数组的第一个位置。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 你的目标是使用最少的跳跃次数到达数组的最后一个位置。
>
> 假设你总是可以到达数组的最后一个位置。
>
>  
>
> **示例 1:**
>
> ```
> 输入: nums = [2,3,1,1,4]
> 输出: 2
> 解释: 跳到最后一个位置的最小跳跃数是 2。
>      从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
> ```
>
> **示例 2:**
>
> ```
> 输入: nums = [2,3,0,1,4]
> 输出: 2
> ```
>
>  
>
> **提示:**
>
> - `1 <= nums.length <= 104`
> - `0 <= nums[i] <= 1000`

accept 答案：

```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        # 尝试和跳跃游戏1一样的方法，但引入记事本，遍历所有可能的方法然后寻找最小的步数
        paths = []
        step = 0 # 现在的位置
        paths.append(step)
        end = len(nums) - 1
        while step < end:
            if step + nums[step] >= end:
                paths.append(end)
                step = end
                break
            else:
                if nums[step] == 0: # 该位置无法起跳，False
                    break
                maxx = -1 # 最远能跳多远
                next_jump = -1 # 选定的起跳位置
                for jump in range(1, nums[step]+1):
                    if jump + step >= len(nums):
                        break
                    if jump + step + nums[jump + step] > maxx:
                        maxx = jump + step + nums[jump + step]
                        next_jump = jump
                # 至此，已经找到了下次能跳的最远的位置，跳jump次
                step += next_jump
                paths.append(step)
        # 这里注意一个边界条件，step == end的时候，没有进循环，但已经到达最后一个坐标了
        return len(paths) - 1
```

## 53. 最大子数组和

> 给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
>
> **子数组** 是数组中的一个连续部分。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
> 输出：6
> 解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [1]
> 输出：1
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [5,4,-1,7,8]
> 输出：23
> ```
>
>  
>
> **提示：**
>
> - `1 <= nums.length <= 10^5`
> - `-10^4 <= nums[i] <= 10^4`

Accept答案：

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        '''
        思路：动态规划——复习一下动态规划的要点
        1. 拆分子问题：re = max_{i=0:n-1} f(i), 以i为结尾的连续子数组最大和
        2. 构造状态转移方程f(i) = max{f(i-1)+nums[i], nums[i]}
        3. 确定边界 f(0) = nums[0]
        4. 从子问题计算并推广
        '''
        all_re = []
        for i in range(len(nums)):
            if i == 0:
                all_re.append(nums[i])
            else:
                all_re.append(max([all_re[-1]+nums[i], nums[i]]))
        return max(all_re)
```

## 62. 不同路径

>  一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。
>
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。
>
> 问总共有多少条不同的路径？
>
>  
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)
>
> ```
> 输入：m = 3, n = 7
> 输出：28
> ```
>
> **示例 2：**
>
> ```
> 输入：m = 3, n = 2
> 输出：3
> 解释：
> 从左上角开始，总共有 3 条路径可以到达右下角。
> 1. 向右 -> 向下 -> 向下
> 2. 向下 -> 向下 -> 向右
> 3. 向下 -> 向右 -> 向下
> ```
>
> **示例 3：**
>
> ```
> 输入：m = 7, n = 3
> 输出：28
> ```
>
> **示例 4：**
>
> ```
> 输入：m = 3, n = 3
> 输出：6
> ```
>
>  
>
> **提示：**
>
> - `1 <= m, n <= 100`
> - 题目数据保证答案小于等于 `2 * 10^9`

accept答案：

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        '''
        感觉是动态规划
        1. 拆分子问题：f(m, n) = f(m-1, n) + f(m, n-1)
        2. 状态转移方程: 
            f(i, j) = f(i-1, j) + f(i, j-1), i != 0, j != 0
            f(0, j) = f(0, j-1), j != 0
            f(i, 0) = f(i-1, 0), i != 0
        3. 边界: f(0, 0) = 1
        4. 求解
        '''
        re = {}
        re['0_0'] = 1
        # 填充第一行
        for j in range(n):
            if j == 0:
                continue
            else:
                re[f'{0}_{j}'] = re[f'{0}_{j-1}']
        # 填充第一列
        for i in range(m):
            if i == 0:
                continue
            else:
                re[f'{i}_{0}'] = re[f'{i-1}_{0}']
        # 填充其他位置
        for i in range(m):
            for j in range(n):
                if i == 0 or j == 0:
                    continue
                else:
                    re[f'{i}_{j}'] = re[f'{i-1}_{j}'] + re[f'{i}_{j-1}']
        return re[f'{m-1}_{n-1}']
```

## 64. 最小路径和

> 给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
>
> **说明：**每次只能向下或者向右移动一步。
>
>  
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)
>
> ```
> 输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
> 输出：7
> 解释：因为路径 1→3→1→1→1 的总和最小。
> ```
>
> **示例 2：**
>
> ```
> 输入：grid = [[1,2,3],[4,5,6]]
> 输出：12
> ```
>
>  
>
> **提示：**
>
> - `m == grid.length`
> - `n == grid[i].length`
> - `1 <= m, n <= 200`
> - `0 <= grid[i][j] <= 100`

accept答案：

```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        '''
        动态规划：
        1. 拆分子问题 
            f(m,n)是到达(m,n)点的最小路径和，
            f(m,n) = min{f(m-1,n), f(m,n-1)} + grid[m][n]
        2. 状态转移方程
            f(i,j) = min{f(i-1,j), f(i,j-1)} + grid[i][j], i, j != 0
            f(0,j) = f(0,j-1) + grid[0][j], j != 0
            f(i,0) = f(i-1,0) + grid[i][0], i != 0
        2. 边界
            f(0,0) = gird[0][0]
        4. 求解
        '''
        m, n = len(grid), len(grid[0])
        re = {}
        for i in range(m):
            for j in range(n):
                if i == 0 and j == 0:
                    re[f'{i}_{j}'] = grid[i][j]
                elif i == 0 and j != 0:
                    re[f'{i}_{j}'] = re[f'{i}_{j-1}'] + grid[i][j]
                elif i != 0 and j == 0:
                    re[f'{i}_{j}'] = re[f'{i-1}_{j}'] + grid[i][j]
                else:
                    re[f'{i}_{j}'] = min([re[f'{i-1}_{j}'], re[f'{i}_{j-1}']]) + grid[i][j]
        return re[f'{m-1}_{n-1}']
```

## 70. 爬楼梯

> 假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。
>
> 每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？
>
>  
>
> **示例 1：**
>
> ```
> 输入：n = 2
> 输出：2
> 解释：有两种方法可以爬到楼顶。
> 1. 1 阶 + 1 阶
> 2. 2 阶
> ```
>
> **示例 2：**
>
> ```
> 输入：n = 3
> 输出：3
> 解释：有三种方法可以爬到楼顶。
> 1. 1 阶 + 1 阶 + 1 阶
> 2. 1 阶 + 2 阶
> 3. 2 阶 + 1 阶
> ```
>
>  
>
> **提示：**
>
> - `1 <= n <= 45`

accept 答案：

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        '''
        虽然是easy但好像可以动态规划
        1. 分解问题
            f(n)到第n阶的时的爬楼方法总数
            f(n) = f(n-1) + f(n-2)
        2. 状态转移方程
            f(i) = f(i-1) + f(i-2)
        3. 边界
            f(1) = 1
            f(2) = 2 # 1+1 / 2
        4. 求解
        '''
        re = {}
        re[1] = 1
        re[2] = 2
        for i in range(1, n+1):
            if i == 1 or i == 2:
                continue
            else:
                re[i] = re[i-1] + re[i-2]
        return re[n]
```

## 78. 子集

> 给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。
>
> 解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。
>
>  
>
> **示例 1：**
>
> ```
> 输入：nums = [1,2,3]
> 输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [0]
> 输出：[[],[0]]
> ```
>
>  
>
> **提示：**
>
> - `1 <= nums.length <= 10`
> - `-10 <= nums[i] <= 10`
> - `nums` 中的所有元素 **互不相同**

accept答案：

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        '''
        问题拆解为：
        所有有nums[i]参与的非空子集，都是由nums[i]之前的数字组成的子集中在分别加入nums[i]
        f(nums[final]) = f(nums[final-1]).item.append(nums[final]) + [nums[final]]
        '''
        re = []
        for i in range(len(nums)):
            if i > 0:
                new_re = []
                for j in range(len(re)):
                    new_re.append(re[j] + [nums[i]])
                re = re + new_re
            re.append([nums[i]]) # 自己
        re.append([])
        return re
```

## 121. 买卖股票的最佳时机

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。
>
> 你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。
>
> 返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。
>
>  
>
> **示例 1：**
>
> ```
> 输入：[7,1,5,3,6,4]
> 输出：5
> 解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
>      注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
> ```
>
> **示例 2：**
>
> ```
> 输入：prices = [7,6,4,3,1]
> 输出：0
> 解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
> ```
>
>  
>
> **提示：**
>
> - `1 <= prices.length <= 105`
> - `0 <= prices[i] <= 104`

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        '''
        思路: length很大，应该不能用暴力
        在第i天买入，j天卖出，利润为prices[j] - prices[i], i < j
        第i天卖出获得的最大收益f(i) = price[i] - i天之前的最小买入值g[i-1]
        g[i-1] = min(g[i-2], nums[i-1])
        动态规划的是i之前最小买入值
        '''
        minn = prices[0] # 最小买入
        maxx = 0 # 最大收益
        for i in range(1, len(prices)):
            ref = prices[i] - minn # i
            minn = min([minn, prices[i]])
            maxx = max([maxx, ref])
        return maxx
```

## 139. 单词拆分 <a name="split_words">📌</a>

> 给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。请你判断是否可以利用字典中出现的单词拼接出 `s` 。
>
> **注意：**不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。
>
>  
>
> **示例 1：**
>
> ```
> 输入: s = "leetcode", wordDict = ["leet", "code"]
> 输出: true
> 解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。
> ```
>
> **示例 2：**
>
> ```
> 输入: s = "applepenapple", wordDict = ["apple", "pen"]
> 输出: true
> 解释: 返回 true 因为 "applepenapple" 可以由 "apple" "pen" "apple" 拼接成。
>      注意，你可以重复使用字典中的单词。
> ```
>
> **示例 3：**
>
> ```
> 输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
> 输出: false
> ```
>
>  
>
> **提示：**
>
> - `1 <= s.length <= 300`
> - `1 <= wordDict.length <= 1000`
> - `1 <= wordDict[i].length <= 20`
> - `s` 和 `wordDict[i]` 仅有小写英文字母组成
> - `wordDict` 中的所有字符串 **互不相同**

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        '''
        思路：动态规划，一个合法的词语从第一个位置开始就属于一个词
        f[i]: s[0:i-1]可否被拆分为合法的词
        f[j] = f[i] && check[s[i:j]], check[s[i:j]]:s[i:j]是否属于一个词
        注意，对于任意一个点j，需要枚举每个合法的i，以检查其合法性
        '''
        res = [False] * (len(s)+1)
        words = set(wordDict)
        res[0] = True
        for j in range(1, len(s)+1):
            for i in range(0, j):
                if res[i] and s[i:j] in words:
                    res[j] = True

        return res[len(s)]
```

