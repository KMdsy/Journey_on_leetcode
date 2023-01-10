---
title: Dynamic Programming (动态规划) 
date: 2022-08-19 14:00:00
updated: 2023-01-10 23:36:00
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

1. [单词拆分](#split_words): 这个题还没搞懂，要反复记忆。

1. [打家劫舍](#rob)：定义一个`f(i)`表示抢劫到第`i`个房子的时候可以抢劫到的最大数量，有两种可能，一种是抢劫了上一间，则此次不能抢劫，一种是此次可以抢劫，则加上本次抢劫的钱。\

    `f(i) = max(f(i-1), f(i-1)+money[i])`

1. [最大的正方形](#max_squre)：对于在矩阵内搜索正方形或长方形的题型，一种常见的做法是定义一个二维`dp `数组，其中`dp[i][j]` 表示满足题目条件的、以`(i, j)` 为右下角的正方形或者长方形的属性。对于本题，则表示以`(i, j)` 为右下角的全由`1 `构成的最大正方形边长。转移矩阵是

    `dp[i][j] = min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1])+1`

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
        f[j] = f[i] && check[s[i:j]], 
        	check[s[i:j]]: s[i:j]是否属于一个词
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

## 198. 打家劫舍 <a name="rob">📌</a>

> 你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。
>
> **示例 1：**
>
> ```
> 输入：[1,2,3,1]
> 输出：4
> 解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
>      偷窃到的最高金额 = 1 + 3 = 4 。
> ```
>
> **示例 2：**
>
> ```
> 输入：[2,7,9,3,1]
> 输出：12
> 解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
>      偷窃到的最高金额 = 2 + 9 + 1 = 12 。 
> ```
>
> **提示：**
>
> - `1 <= nums.length <= 100`
> - `0 <= nums[i] <= 400`

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        '''
        动态规划：
        定义一个f(i)表示抢劫到第i个房子的时候可以抢劫到的最大数量
        ⭐️ 转移方程：f(i) = max(f(i-1), f(i-2)+nums[i])
            - 有两种可能，一种是抢劫了上一间，则此次不能抢劫
            - 一种是此次可以抢劫，则加上本次抢劫的钱
        ⭐️ 边界：
            - f(0) = nums[0] # 本次抢劫
            - f(1) = max(nums[0], nums[1]) # 如果上次抢劫了，这次就不能抢了
        '''
        res = {}
        if len(nums) == 0:
            return 0
        for i in range(len(nums)):
            if i == 0:
                res[i] = nums[0]
            elif i == 1:
                res[i] = max([nums[0], nums[1]])
            else:
                res[i] = max([res[i-1], res[i-2]+nums[i]])
        return res[len(nums)-1]
```

## 413. 等差数列划分

> 如果一个数列 **至少有三个元素** ，并且任意两个相邻元素之差相同，则称该数列为等差数列。
>
> - 例如，`[1,3,5,7,9]`、`[7,7,7,7]` 和 `[3,-1,-5,-9]` 都是等差数列。
>
> 给你一个整数数组 `nums` ，返回数组 `nums` 中所有为等差数组的 **子数组** 个数。
>
> **子数组** 是数组中的一个连续序列。
>
> **示例 1：**
>
> ```
> 输入：nums = [1,2,3,4]
> 输出：3
> 解释：nums 中有三个子等差数组：[1, 2, 3]、[2, 3, 4] 和 [1,2,3,4] 自身。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [1]
> 输出：0
> ```
>
> **提示：**
>
> - `1 <= nums.length <= 5000`
> - `-1000 <= nums[i] <= 1000`

```python
class Solution:
    def numberOfArithmeticSlices(self, nums: List[int]) -> int:
        '''
        动态规划：
        定义以nums[i]为结尾的等差数列的子数组数量为f(i)
        ⭐️ 转移方程：
            - 如果nums[i]与nums[i-1],nums[i-2]可以组成等差序列，则
                f(i) = f(i-1) + 1 
                如果nums[i]能和nums[i-1] nums[i-2]组成等差数列，
                则以nums[i-1]结尾的等差数列均可以nums[i]结尾，(以任意位置开始，并至少有4个元素)
                且多了一个新等差数列[nums[i],nums[i-1],nums[i-2]], （最新的一个三个元素组成的子数列）
        ⭐️ 边界条件
            f(0) = f(1) = 0
        ⭐️ 最后返回所有解之和
        '''
        res = [0] * len(nums)
        for i in range(len(nums)):
            if i == 0 or i == 1:
                res[i] = 0
            else:
                if nums[i] - nums[i-1] == nums[i-1] - nums[i-2]:
                    res[i] = res[i-1] + 1
        return sum(res)
```

## 542. 01 矩阵

> 给定一个由 `0` 和 `1` 组成的矩阵 `mat` ，请输出一个大小相同的矩阵，其中每一个格子是 `mat` 中对应位置元素到最近的 `0` 的距离。
>
> 两个相邻元素间的距离为 `1` 。
>
> **示例 1：**
>
> ![img](https://pic.leetcode-cn.com/1626667201-NCWmuP-image.png)
>
> ```
> 输入：mat = [[0,0,0],[0,1,0],[0,0,0]]
> 输出：[[0,0,0],[0,1,0],[0,0,0]]
> ```
>
> **示例 2：**
>
> ![img](https://pic.leetcode-cn.com/1626667205-xFxIeK-image.png)
>
> ```
> 输入：mat = [[0,0,0],[0,1,0],[1,1,1]]
> 输出：[[0,0,0],[0,1,0],[1,2,1]] 
> ```
>
> **提示：**
>
> - `m == mat.length`
> - `n == mat[i].length`
> - `1 <= m, n <= 10^4`
> - `1 <= m * n <= 10^4`
> - `mat[i][j] is either 0 or 1.`
> - `mat` 中至少有一个 `0 `

解法1：从0出发的广度优先搜索

```python
class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:
        # 方法一：从0开始的广度优先搜索
        '''
        从每个为0的位置出发，这些为止的结果都是0
        1. 遍历每个0位置的非0邻居，如果这些邻居没有被遍历过，那这些非0元素的邻居的结果就是1
        2. 遍历每个非0位置的邻居，如果这些邻居没有被遍历过，那这些为止的结果就是他们的邻居结果+1
        3. 以此类推
        '''
        # 1. 先为每个0的位置赋结果
        # 设立一个队列q，存储遍历过的位置
        q = []
        m, n = len(mat), len(mat[0])
        # res = [[1e8] * n] * m # 这种内存分配方式是不对的，在外层*m的时候，会将内存直接复制
        res = {}
        for i in range(m):
            for j in range(n):
                if mat[i][j] == 0:
                    res[(i,j)] = 0
                    q.append((i,j))
        # 2. 开始遍历
        while len(q) > 0:
            pos_i, pos_j = q.pop(0)
            # 遍历邻居
            for i, j in [(pos_i-1, pos_j), (pos_i+1, pos_j), (pos_i, pos_j-1), (pos_i, pos_j+1)]:
                if 0 <= i < m and 0 <= j < n and res.get((i,j)) is None:
                    # 没访问过
                    res[(i,j)] = res[(pos_i, pos_j)] + 1
                    q.append((i, j))
        for (i, j), v in res.items():
            mat[i][j] = v
        return mat
```

解法2：动态规划，

![image-20230106194731357](/Users/shaoyu/Library/Application Support/typora-user-images/image-20230106194731357.png)

```python
  class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:      
  			# 方法2 动态规划
        '''
        对于每个非0的位置，其结果都是其邻居距离0的距离+1
        因此对于一个非0的位置[i,j]，检查他的四个邻居，取邻居的最小值+1即为答案
        🌟 检查四个邻居可以转化为两次扫描，分别为 左上 - 右下、右下 - 左上
        '''
        # 1. 处理所有的0位置
        res = {}
        m, n = len(mat), len(mat[0])
        for i in range(m):
            for j in range(n):
                if mat[i][j] == 0:
                    res[(i,j)] = 0
        # 遍历 左上 - 右下
        for i in range(m):
            for j in range(n):
                if mat[i][j] == 1:
                    res[(i,j)] = min([res.get((i-1,j), 1e8) + 1, res.get((i,j-1), 1e8) + 1])
        # 遍历 右下 - 左上
        for i in range(m-1, -1, -1):
            for j in range(n-1, -1, -1):
                if mat[i][j] == 1:
                    res[(i,j)] = min([res.get((i+1,j), 1e8) + 1, res.get((i,j+1), 1e8) + 1, res[(i,j)]])
        for (i, j), v in res.items():
            mat[i][j] = v
        return mat  
```

## 221. 最大正方形 <a name="max_squre">📌</a>

> 在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。 
>
> **示例 1：**
>
> ![img](https://assets.leetcode.com/uploads/2020/11/26/max1grid.jpg)
>
> ```
> 输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
> 输出：4
> ```
>
> **示例 2：**
>
> ![img](https://assets.leetcode.com/uploads/2020/11/26/max2grid.jpg)
>
> ```
> 输入：matrix = [["0","1"],["1","0"]]
> 输出：1
> ```
>
> **示例 3：**
>
> ```
> 输入：matrix = [["0"]]
> 输出：0
> ```
>
>  
>
> **提示：**
>
> - `m == matrix.length`
> - `n == matrix[i].length`
> - `1 <= m, n <= 300`
> - `matrix[i][j]` 为 `'0'` 或 `'1'`

```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        '''
        动态规划：
        对于在矩阵内搜索正方形或长方形的题型，一种常见的做法是定义一个二维dp 数组，其中
            dp[i][j] 表示满足题目条件的、以(i, j) 为右下角的正方形或者长方形的属性。
        对于本题，则表示以(i, j) 为右下角的全由1 构成的最大正方形边长
        
        if matrix[i,j] == 0，res[i,j] = 0
        else: res[i, j] = min([res[i-1,j], res[i,j-1], res[i-1,j-1]]) + 1 #转移矩阵，可以直接记住
        '''
        res = {}
        m, n = len(matrix), len(matrix[0])
        for i in range(m):
            for j in range(n):
                if matrix[i][j] == '0':
                    res[(i,j)] = 0
                else:
                    if i == 0 or j == 0:
                        res[(i,j)] = 1
                    else:
                        res[(i,j)] = min([res.get((i-1,j),1e8), res.get((i,j-1),1e8), res.get((i-1,j-1),1e8)]) + 1
        maxx = -1
        for (i, j), v in res.items():
            if v > maxx:
                maxx = v

        return maxx**2
        
        
```

## 279. 完全平方数

> 给你一个整数 `n` ，返回 *和为 `n` 的完全平方数的最少数量* 。
>
> **完全平方数** 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，`1`、`4`、`9` 和 `16` 都是完全平方数，而 `3` 和 `11` 不是。 
>
> **示例 1：**
>
> ```
> 输入：n = 12
> 输出：3 
> 解释：12 = 4 + 4 + 4
> ```
>
> **示例 2：**
>
> ```
> 输入：n = 13
> 输出：2
> 解释：13 = 4 + 9
> ```
>
> **提示：**
>
> - `1 <= n <= 10^4`

![image-20230110231240723](https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230110231240723.png)

```python
class Solution:
    def numSquares(self, n: int) -> int:
        '''
        思路：用dp[i]表示数字i可以最少由多少个平方数表示
        '''
        dp = [0] * (n+1)
        for i in range(1, n+1):
            # 寻找比i小，且用最少的平方数表示的数字
            # 不考虑不能用平方数表示的数字，他们直接用转移方程的解填充即可
            j = 1
            minn = 1e8
            while j**2 <= i:
                if dp[i-j**2] < minn:
                    minn = dp[i-j**2]
                j += 1
            dp[i] = 1 + minn
        return dp[n]
```

## 91. 解码方法

> 一条包含字母 `A-Z` 的消息通过以下映射进行了 **编码** ：
>
> ```
> 'A' -> "1"
> 'B' -> "2"
> ...
> 'Z' -> "26"
> ```
>
> 要 **解码** 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，`"11106"` 可以映射为：
>
> - `"AAJF"` ，将消息分组为 `(1 1 10 6)`
> - `"KJF"` ，将消息分组为 `(11 10 6)`
>
> 注意，消息不能分组为 `(1 11 06)` ，因为 `"06"` 不能映射为 `"F"` ，这是由于 `"6"` 和 `"06"` 在映射中并不等价。
>
> 给你一个只含数字的 **非空** 字符串 `s` ，请计算并返回 **解码** 方法的 **总数** 。
>
> 题目数据保证答案肯定是一个 **32 位** 的整数。 
>
> **示例 1：**
>
> ```
> 输入：s = "12"
> 输出：2
> 解释：它可以解码为 "AB"（1 2）或者 "L"（12）。
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "226"
> 输出：3
> 解释：它可以解码为 "BZ" (2 26), "VF" (22 6), 或者 "BBF" (2 2 6) 。
> ```
>
> **示例 3：**
>
> ```
> 输入：s = "06"
> 输出：0
> 解释："06" 无法映射到 "F" ，因为存在前导零（"6" 和 "06" 并不等价）。 
> ```
>
> **提示：**
>
> - `1 <= s.length <= 100`
> - `s` 只包含数字，并且可能包含前导零。

```python
class Solution:
    def numDecodings(self, s: str) -> int:
        '''
        动态规划：分割，dp[i]表示s[1:i]最多有几种分割方法，
        本次解码是在上次解码的基础上，加上新的字符的解码方法
        新的字符有两种情况：s[i-1:i], s[i-2:i]
        1. 如果s[i-1:i] != '0', 新字符的解码方法为dp[i-1]
        2. 如果s[i-2:i]可以解码为A-Z, 新字符的解码方法为dp[i-2]
        '''
        n = len(s)
        dp = [1] + [0] * n
        for i in range(1, n+1):
            # 检查s[0:i]
            if s[i-1] != '0':
                # 1. 如果s[i-1:i] != '0', 新字符的解码方法为dp[i-1]
                dp[i] += dp[i-1]
            if i > 1 and s[i-2] != '0' and 0 <= int(s[i-2:i]) <= 26:
                # 2. 如果s[i-2:i]可以解码为A-Z, 新字符的解码方法为dp[i-2]
                dp[i] += dp[i-2]
        return dp[-1]
```

