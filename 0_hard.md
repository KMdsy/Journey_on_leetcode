---
title: 数组 Array (medium)
date: 2022-12-11 21:00:00
updated: 2022-12-11 23:00:00
tag:
- leetcode
---

## 本章重点

1. 针对原题中的`O(log(m+n))`时间复杂度要求，应该想到二分查找（[寻找两个正序数组中的中位数](#find_mid)）。

2. [分糖果问题](#sugers)：我们可以将「相邻的孩子中，评分高的孩子必须获得更多的糖果」这句话拆分为两个规则，分别处理。

    ​        左规则：当 i+1 孩子的分数比 i 孩子多，则在 i 孩子的基础上多加1个糖果

    ​        右规则：当 i 孩子的分数比 i+1 孩子多，则在 i+1 孩子的基础上多加1个糖果

    ​        我们遍历该数组两次，处理出每一个学生分别满足左规则或右规则时，最少需要被分得的糖果数量。每个人最终分得的糖果数量即为这两个数量的最大值。

3. 

## 本章题目思路记忆要点

1. 

## 题目

### 4. 寻找两个正序数组的中位数 <a name="find_mid">📌</a>

> 给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。
>
> 算法的时间复杂度应该为 `O(log (m+n))` 。
>
> **示例 1：**
>
> ```
> 输入：nums1 = [1,3], nums2 = [2]
> 输出：2.00000
> 解释：合并数组 = [1,2,3] ，中位数 2
> ```
>
> **示例 2：**
>
> ```
> 输入：nums1 = [1,2], nums2 = [3,4]
> 输出：2.50000
> 解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5 
> ```
>
> **提示：**
>
> - `nums1.length == m`
> - `nums2.length == n`
> - `0 <= m <= 1000`
> - `0 <= n <= 1000`
> - `1 <= m + n <= 2000`
> - `-10^6 <= nums1[i], nums2[i] <= 10^6`

accept答案：

```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        '''
        思路：从复杂度方面考虑，不可以全部扫描，这种方法的时间复杂度是 O(m+n)
        这里考虑：用一个栈来顺序存储合并数组，栈的大小为
        (m+n)/2 -> (m+n)%2 == 0
        (m+n-1)/2 -> (m+n)%2 != 0
        '''
        ordered = []
        pin1, pin2 = 0, 0
        m, n = len(nums1), len(nums2)
        length = (m+n-1)/2 if (m+n)%2 != 0 else (m+n)/2
        while len(ordered) < length+1:
            if pin1 < m and pin2 < n and nums1[pin1] <= nums2[pin2]:
                # 相等的时候只放进去一个
                ordered.append(nums1[pin1])
                pin1 += 1
            elif pin1 < m and pin2 < n:
                ordered.append(nums2[pin2])
                pin2 += 1
            else:
                # 指针有一个不在范围了
                if pin1 >= m and pin2 < n:
                    ordered.append(nums2[pin2])
                    pin2 += 1
                elif pin1 < m and pin2 >= n:
                    ordered.append(nums1[pin1])
                    pin1 += 1
                else:
                    break
        if (m+n)%2 == 0:
            return (ordered[-1] + ordered[-2])/2
        else:
            return ordered[-1]
```

针对原题中的`O(log(m+n))`时间复杂度要求，应该想到二分查找

### 135.分发糖果 <a name="sugers">📌</a>

> `n` 个孩子站成一排。给你一个整数数组 `ratings` 表示每个孩子的评分。
>
> 你需要按照以下要求，给这些孩子分发糖果：
>
> - 每个孩子至少分配到 `1` 个糖果。
> - 相邻两个孩子评分更高的孩子会获得更多的糖果。
>
> 请你给每个孩子分发糖果，计算并返回需要准备的 **最少糖果数目** 。
>
> **示例 1：**
>
> ```
> 输入：ratings = [1,0,2]
> 输出：5
> 解释：你可以分别给第一个、第二个、第三个孩子分发 2、1、2 颗糖果。
> ```
>
> **示例 2：**
>
> ```
> 输入：ratings = [1,2,2]
> 输出：4
> 解释：你可以分别给第一个、第二个、第三个孩子分发 1、2、1 颗糖果。
>      第三个孩子只得到 1 颗糖果，这满足题面中的两个条件。
> ```
>
> **提示：**
>
> - `n == ratings.length`
> - `1 <= n <= 2 * 104`
> - `0 <= ratings[i] <= 2 * 104`

```python
class Solution:
    def candy(self, ratings: List[int]) -> int:
        '''
        我们可以将「相邻的孩子中，评分高的孩子必须获得更多的糖果」这句话拆分为两个规则，分别处理。
        左规则：当 i+1 孩子的分数比 i 孩子多，则在 i 孩子的基础上多加1个糖果
        右规则：当 i 孩子的分数比 i+1 孩子多，则在 i+1 孩子的基础上多加1个糖果
        我们遍历该数组两次，处理出每一个学生分别满足左规则或右规则时，最少需要被分得的糖果数量。每个人最终分得的糖果数量即为这两个数量的最大值。
        '''
        res1 = []
        for i, rate in enumerate(ratings):
            if i == 0:
                res1.append(1)
            else:
                if rate > ratings[i-1]:
                    res1.append(res1[-1]+1) # 比前一个多一个
                else:
                    res1.append(1)
        res2 = []
        ratings_inver = ratings[::-1]
        for i, rate in enumerate(ratings_inver):
            if i == 0:
                res2.append(1)
            else:
                if rate > ratings_inver[i-1]:
                    res2.append(res2[-1]+1) # 比前一个多一个
                else:
                    res2.append(1)
        res2 = res2[::-1]
        res = []
        for num1, num2 in zip(res1, res2):
            res.append(max([num1, num2]))
        return sum(res)
```

