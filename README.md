---
title: 从零开始的leetcode之旅
date: 2022-04-11 10:12:00
updated: 2022-05-18 21:02:00
tag:
- leetcode
---

学习之路总结：

**子晗**：按照数据结构（如数组、哈希表等）过每个种类的题，每个15-20题左右。加上每日一题的训练。学习重点在动态规划、二叉树和图，其中图的重点是深度搜索和广度搜索。

**🌟一个小目标**：先刷400道✌️

**🌟一个flag**：每次都要在这里记录收获哦～



## 目录

0. [python语法查漏补缺](./python.md)
1. Leetcode Top 100: [easy](./0_array_easy.md), [medium](./0_array_medium.md)



## 刷题之旅从现在开始

1. **2022.04.11**

    两数之和（[Two Sum](https://leetcode-cn.com/problems/two-sum/)）、回文数（[Palindrome Number](https://leetcode-cn.com/problems/palindrome-number/)）、罗马数字转整数（[Roman to Integer](https://leetcode-cn.com/problems/roman-to-integer/)）、最长公共前缀（[Longest Common Prefix](https://leetcode-cn.com/problems/longest-common-prefix/)）、有效的括号（[Valid Parentheses](https://leetcode-cn.com/problems/valid-parentheses/)）

2. **2022.04.13**

    合并两个有序列表（[Merge Two Sorted Lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)）、删除有序数组中的重复项（[Remove Duplicates from Sorted Array](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)）、移除元素（[Remove Element](https://leetcode-cn.com/problems/remove-element/)）、实现 strStr()（[Implement strStr()](https://leetcode-cn.com/problems/implement-strstr/)）、搜索插入位置（[Search Insert Position](https://leetcode-cn.com/problems/search-insert-position/)）

3. **2022.04.14**

    两数相加（[Add Two Numbers](https://leetcode-cn.com/problems/add-two-numbers/)）

4. **2022.04.17**

    无重复字符的最长子串（[Longest Substring Without Repeating Characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)）、最长回文子串（[Longest Palindromic Substring](https://leetcode-cn.com/problems/longest-palindromic-substring/)）

5. **2022.04.27**

    Z字形变换（[ ZigZag Conversion](https://leetcode-cn.com/problems/zigzag-conversion/)）、盛最多水的容器（[Container With Most Water](https://leetcode-cn.com/problems/container-with-most-water/)）、整数转罗马数字（[Integer to Roman](https://leetcode-cn.com/problems/integer-to-roman/)）

6. **2022.04.28**

    三数之和（[3Sum](https://leetcode-cn.com/problems/3sum/)）、最接近的三数之和（[3Sum Closest](https://leetcode-cn.com/problems/3sum-closest/)）、电话号码的字母组合（[Letter Combinations of a Phone Number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)）

7. **2022.04.29**

    整数翻转（[Reverse Integer](https://leetcode-cn.com/problems/reverse-integer/)）、字符串转整数（[String to Integer (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)）、四数之和（[4Sum](https://leetcode-cn.com/problems/4sum/)）、删除链表的倒数第N个节点（[Remove Nth Node From End of List](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)）、生成括号（[Generate Parentheses](https://leetcode-cn.com/problems/generate-parentheses/)）

8. **2022.05.05**

    两两交换列表中的节点（[Swap Nodes in Pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)）、两数相除（[Divide Two Integers](https://leetcode-cn.com/problems/divide-two-integers/)）

9. **2022.05.12**

    全排列（[Permutations](https://leetcode.cn/problems/permutations/)）、全排列II（[Permutations II](https://leetcode.cn/problems/permutations-ii/)）、旋转图像（[Rotate Image](https://leetcode.cn/problems/rotate-image/)）Pow(x, n)（[ Pow(x, n)](https://leetcode.cn/problems/powx-n/)）、字母异位词分组（[Group Anagrams](https://leetcode.cn/problems/group-anagrams/)）、字符串相乘（[Multiply Strings](https://leetcode.cn/problems/multiply-strings/)）、组合总和（[Combination Sum](https://leetcode.cn/problems/combination-sum/)）

10. **2022.05.13**

    组合总和（[ Combination Sum II](https://leetcode.cn/problems/combination-sum-ii/)）、外观数列（[Count and Say](https://leetcode.cn/problems/count-and-say/)）

11. **2022.05.18**

    相同的树（[Same Tree](https://leetcode.cn/problems/same-tree/)）



## 重点算法索引

1. 单模字符串匹配算法（[寻找子串问题](./0_array_easy.md#strstr)）：1⃣️ Sunday算法，[自己写的思路](./0_array_easy.md#sunday)，[leetcode](https://leetcode-cn.com/problems/implement-strstr/solution/python3-sundayjie-fa-9996-by-tes/)；2⃣️ KMP算法，[leetcode](https://leetcode-cn.com/problems/implement-strstr/solution/shua-chuan-lc-shuang-bai-po-su-jie-fa-km-tb86/)
2. 动态规划算法 [link](https://zhuanlan.zhihu.com/p/365698607)：
    1. [最长回文子串（入门）](./0_array_medium.md#dynamic_programing)
3. 回溯 backtrack
    1. [电话号码的所有组合](./0_array_medium.md#comb_phonenumber)
    2. [组合总和](./0_array_medium.md#comb_sum)、[组合总和II](./0_array_medium.md#comb_sum2)

4. 二分法 + 快速乘法
    1. [两数相除问题](./0_array_medium.md#two_divide)


5. 树的深度优先和广度优先搜索
    1. [相同的树](./0_array_easy.md#same_tree)

## TODO

1. 学习Knuth-Morris-Pratt（KMP）算法，并完善在array_easy文档（2022.04.13）
2. 动态规划，看着思路写一遍代码，完善在array_medium文档（2022.04.17）
3. 系统总结一下回溯（backtrack）方法的适用类型（2022.04.28）
4. 有限状态自动机（[字符串转整数问题](https://leetcode-cn.com/problems/string-to-integer-atoi/solution/zi-fu-chuan-zhuan-huan-zheng-shu-atoi-by-leetcode-/)）(2022.04.29)
5. 用递归实现链表的节点两两交换：[link](https://leetcode-cn.com/problems/swap-nodes-in-pairs/solution/liang-liang-jiao-huan-lian-biao-zhong-de-jie-di-91/)（2022.05.05）
6. 彻底搞定二分法，以及向上/向下取整对代码的影响（[两数相除问题](./0_array_medium.md#two_divide)），题解：[link](https://leetcode-cn.com/problems/divide-two-integers/solution/shua-chuan-lc-er-fen-bei-zeng-cheng-fa-j-m73b/)（2022.05.05）
7. 今日所有题目都没有在文章中总结记忆点/重点，记得补上。对了，还没看题解，需要过一遍。（2022.05.12/13）
