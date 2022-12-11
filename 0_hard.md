---
title: 数组 Array (medium)
date: 2022-12-11 21:00:00
updated: 2022-12-11 23:00:00
tag:
- leetcode
---

## 本章重点

1. 针对原题中的`O(log(m+n))`时间复杂度要求，应该想到二分查找（[寻找两个正序数组中的中位数](#find_mid)）。

## 本章题目思路记忆要点

1. **两数相加**：储存

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
