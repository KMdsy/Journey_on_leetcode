---
title: Dynamic Programming (动态规划) 
date: 2022-08-19 14:00:00
updated: 2022-08-19 17:00:00
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
1. 

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



