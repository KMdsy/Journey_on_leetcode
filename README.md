---
title: 从零开始的leetcode之旅
date: 2022-04-11 10:12:00
updated: 2022-02-11 23:02:00
tag:
- leetcode
---

学习之路总结：

**子晗**：按照数据结构（如数组、哈希表等）过每个种类的题，每个15-20题左右。加上每日一题的训练。学习重点在动态规划、二叉树和图，其中图的重点是深度搜索和广度搜索。

**🌟一个小目标**：先刷400道✌️

**🌟一个flag**：每次都要在这里记录收获哦～

- Hot100进度过半，开始刷剑指offer
    - **2022.02.10:** 剑指第一天的两个easy，好像没搞懂官方题解的用意，看看✅
    - **2022.02.11**: LFU算法，看懂了但是没有AC，完了看看
    - **2022.02.12**: [买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/description/) DP写对了，但超时了，看看优化方法
    - **2022.02.13**: 结合LFU对collections中的几个哈希字典的概念弄清
    - **2022.02.14**: 一直剩下的回文没搞定，明天来办公室第一件事是将这两天遗留的问题总结一下

- 多在牛客上看看**情景题、应用题**



## 目录

0. [python语法查漏补缺](./python.md)
1. Leetcode Top 100: [easy](./0_array_easy.md), [medium](./0_array_medium.md)



## 刷题之旅从现在开始

TODOs (Updated at 2023.01.23)

从2022.12.22开始，跟着labuladong的算法小抄开始，学架构，并按照后文以及leetcode 101的题目列表开始刷类型题

- 动态规划 
    - 分割类问题
        - 理解一下二分查找的方法：https://leetcode.cn/problems/longest-increasing-subsequence/solutions/147667/zui-chang-shang-sheng-zi-xu-lie-by-leetcode-soluti/
    - 背包问题
        - 理解01背包的优化方法✅
        - 完全背包问题✅
    - labuladong: 子序列类型问题套路✅
    - labuladong: 贪心类型问题
        - [如何运用贪心思想玩跳跃游戏](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485087&idx=1&sn=ddbed992e5ad8f1aa3b3d4afcb17889b&scene=21#wechat_redirect)✅
        - [扫描线技巧：安排会议室](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247491640&idx=1&sn=60344b057f19a4765a15ed17cb7c8018&scene=21#wechat_redirect)
        - [当老司机学会了贪心算法](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247491696&idx=1&sn=f3ddc1f4a7e07e4184aa08f295c7e02c&scene=21#wechat_redirect)
    - labuladong: 用动态规划玩游戏


- 树、图
- 分治
- 双指针
- 二分查找


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
6. 贪心算法

    1. [跳跃游戏II](./dynamic_programming/#跳跃游戏 II)


## TODO

1. 学习Knuth-Morris-Pratt（KMP）算法，并完善在array_easy文档（2022.04.13）
2. 动态规划，看着思路写一遍代码，完善在array_medium文档（2022.04.17）
3. 系统总结一下回溯（backtrack）方法的适用类型（2022.04.28）
4. 有限状态自动机（[字符串转整数问题](https://leetcode-cn.com/problems/string-to-integer-atoi/solution/zi-fu-chuan-zhuan-huan-zheng-shu-atoi-by-leetcode-/)）(2022.04.29)
5. 用递归实现链表的节点两两交换：[link](https://leetcode-cn.com/problems/swap-nodes-in-pairs/solution/liang-liang-jiao-huan-lian-biao-zhong-de-jie-di-91/)（2022.05.05）
6. 彻底搞定二分法，以及向上/向下取整对代码的影响（[两数相除问题](./0_array_medium.md#two_divide)），题解：[link](https://leetcode-cn.com/problems/divide-two-integers/solution/shua-chuan-lc-er-fen-bei-zeng-cheng-fa-j-m73b/)（2022.05.05）
7. 今日所有题目都没有在文章中总结记忆点/重点，记得补上。对了，还没看题解，需要过一遍。（2022.05.12/13）
8. 
