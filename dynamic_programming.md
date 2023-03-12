---
title: Dynamic Programming (动态规划) 
date: 2022-08-19 14:00:00
updated: 2023-02-27 11:00:00
tag:
- leetcode
- dynamic programming
---

## 1. 算法思路

动态规划（Dynamic programming，DP），是一种在数学、管理科学、计算机科学、经济学和生物信息学中使用的，通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。动态规划常常适用于有重叠子问题和最优子结构性质的问题。

以上定义来自维基百科，看定义感觉还是有点抽象。简单来说，动态规划其实就是，给定一个问题，我们把它**拆成一个个子问题**，直到子问题可以直接解决。然后呢，**把子问题答案保存起来**，以减少重复计算。再**根据子问题答案反推**，得出原问题解的一种方法。

动态规划有几个典型特征，**最优子结构、状态转移方程、边界、重叠子问题**。在青蛙跳阶问题中：

- f(n-1)和f(n-2) 称为 f(n) 的最优子结构
- f(n)= f（n-1）+f（n-2）就称为状态转移方程
- f(1) = 1, f(2) = 2 就是边界啦
- 比如f(10)= f(9)+f(8),f(9) = f(8) + f(7) ,f(8)就是重叠子问题。



（引自[link](https://zhuanlan.zhihu.com/p/365698607)）



## 2. 题目思路记忆要点

1. [跳跃游戏I/II](#jump_1)：贪心算法，在每个位置上先评估是否可以一次跳跃到终点，如果不能，则在可以跳跃的位置处分别评估第二跳可以到达的最远位置，来选择下一跳的位置。跳跃游戏II也是这个原理

2. [不同路径I/II](#different_route)：`dp[i][j]`定义了到达`[i][j]`的 路径总数，在上面和左边没有任何障碍的时候，`dp[i][j] = dp[i-1][j] + dp[i][j-1]`

3. [单词拆分](#split_words): 这个题还没搞懂，要反复记忆。

4. [打家劫舍](#rob)：定义一个`f(i)`表示抢劫到第`i`个房子的时候可以抢劫到的最大数量，有两种可能，一种是抢劫了上一间，则此次不能抢劫，一种是此次可以抢劫，则加上本次抢劫的钱。

    `f(i) = max(f(i-1), f(i-1)+money[i])`

5. [最大的正方形](#max_squre)：对于在矩阵内搜索正方形或长方形的题型，一种常见的做法是定义一个二维`dp `数组，其中`dp[i][j]` 表示满足题目条件的、以`(i, j)` 为右下角的正方形或者长方形的属性。对于本题，则表示以`(i, j)` 为右下角的全由`1 `构成的最大正方形边长。转移矩阵是

    `dp[i][j] = min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1])+1`

6. 股票买卖问题

    1. [股票买卖1](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/description/): 只交易一次，则第`i`天的收益即为`prices[i] - min(prices[0:i])`，注意其中的`min`操作会导致超时，用缓存变量解决

    2. [股票买卖2](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/): 不限定交易次数，等价于选择不重合的多个区间`[i, j)`，使得 `\sum (price[j] - price[i])`最大化，这里涉及一个数学问题：`price[j] - price[i] = price[j] - price[j-1] + price[j-1] - price[j-2] + ... + price[i+1] - price[i]`，因此问题转化为，求多个长度为1的区间，这些区间的总和加起来最大。

    3. [股票买卖3](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/): 可以使用动态规划，但会超时，官方解答为

        <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230212180540365.png" alt="image-20230212180540365" style="zoom:25%;" />

7. [石子游戏II](#stoneGameII)

```python
看起来像是动态规划
- 首先一定要存储的是取到某一个位置时，已经得到的最大值或者后面能得到的最大值，
- 但是光有位置是不够的，相同的位置有不同数量的堆可以取，所以我们还需存储当前的M值。

由于本题中的状态是从后往前递推的，
如：假如最后只剩一堆，一定能算出来最佳方案，但是剩很多堆时不好算（依赖后面的状态）。所以我们选择从后往前递推。

dp[i][j]表示剩余[i : len - 1]堆时，M = j的情况下，先取的人能获得的最多石子
- i + 2M >= len, dp[i][M] = sum[i : len - 1], 剩下的堆数能够直接全部取走，那么最优的情况就是剩下的石子总和
- i + 2M < len, dp[i][M] = max(dp[i][M], sum[i : len - 1] - dp[i + x][max(M, x)]), 
- 其中 1 <= x <= 2M，剩下的堆数不能全部取走，那么最优情况就是让下一个人取的更少。
- 对于我所有的x取值，下一个人从x开始取起，M变为max(M, x)，所以下一个人能取dp[i + x][max(M, x)]，
我最多能取sum[i : len - 1] - dp[i + x][max(M, x)]。
- result = dp[0][1]
```





### 2.1 子序列问题(一维dp数组:1143, 300, 二维dp数组: 1312,516,72,5)

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230123001031189.png" alt="image-20230123001031189" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230123001048885.png" alt="image-20230123001048885" style="zoom:50%;" />

### 2.2 背包问题(0-1: 416, 474; 完全: 322, 518)

> 给你一个可装载重量为`W`的背包和`N`个物品，每个物品有重量和价值两个属性。其中第`i`个物品的重量为`wt[i]`，价值为`val[i]`，现在让你用这个背包装物品，最多能装的价值是多少？

**第一步要明确两点，「状态」和「选择」。**

先说状态，如何才能描述一个问题局面？只要给定几个可选物品和一个背包的容量限制，就形成了一个背包问题，对不对？**所以状态有两个，就是「背包的容量」和「可选择的物品」**。

再说选择，也很容易想到啊，对于每件物品，你能选择什么？**选择就是「装进背包」或者「不装进背包」嘛**。

明白了状态和选择，动态规划问题基本上就解决了，只要往这个框架套就完事儿了：

```
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 择优(选择1，选择2...)
```

**第二步要明确`dp`数组的定义。**

`dp`数组是什么？其实就是描述问题局面的一个数组。换句话说，我们刚才明确问题有什么「状态」，现在需要用`dp`数组把状态表示出来。

首先看看刚才找到的「状态」，有两个，也就是说我们需要一个二维`dp`数组，一维表示可选择的物品，一维表示背包的容量。

**`dp[i][w]`的定义如下：对于前`i`个物品，当前背包的容量为`w`，这种情况下可以装的最大价值是`dp[i][w]`。**

PS：为什么要这么定义？便于状态转移，或者说这就是套路，记下来就行了。建议看一下我们的动态规划系列文章，几种动规套路都被扒得清清楚楚了。

**根据这个定义，我们想求的最终答案就是`dp[N][W]`。base case 就是`dp[0][..] = dp[..][0] = 0`**，因为没有物品或者背包没有空间的时候，能装的最大价值就是 0。

细化上面的框架：

```python
int dp[N+1][W+1]
dp[0][..] = 0
dp[..][0] = 0

for i in [1..N]:
    for w in [1..W]:
        dp[i][w] = max(
            把物品 i 装进背包,
            不把物品 i 装进背包
        )
return dp[N][W]
```

**第三步，根据「选择」，思考状态转移的逻辑**。

简单说就是，上面伪码中「把物品`i`装进背包」和「不把物品`i`装进背包」怎么用代码体现出来呢？

**这一步要结合对`dp`数组的定义和我们的算法逻辑来分析：**

先重申一下刚才我们的`dp`数组的定义：

`dp[i][w]`表示：对于前`i`个物品，当前背包的容量为`w`时，这种情况下可以装下的最大价值是`dp[i][w]`。

**如果你没有把这第`i`个物品装入背包**，那么很显然，最大价值`dp[i][w]`应该等于`dp[i-1][w]`。你不装嘛，那就继承之前的结果。

**如果你把这第`i`个物品装入了背包**，那么`dp[i][w]`应该等于`dp[i-1][w-wt[i-1]] + val[i-1]`。

首先，由于`i`是从 1 开始的，所以对`val`和`wt`的取值是`i-1`。

而`dp[i-1][w-wt[i-1]]`也很好理解：你如果想装第`i`个物品，你怎么计算这时候的最大价值？**换句话说，在装第`i`个物品的前提下，背包能装的最大价值是多少？**

显然，你应该寻求剩余重量`w-wt[i-1]`限制下能装的最大价值，加上第`i`个物品的价值`val[i-1]`，这就是装第`i`个物品的前提下，背包可以装的最大价值。

综上就是两种选择，我们都已经分析完毕，也就是写出来了状态转移方程，可以进一步细化代码：

```python
for i in [1..N]:
    for w in [1..W]:
        dp[i][w] = max(
            dp[i-1][w],
            dp[i-1][w - wt[i-1]] + val[i-1]
        )
return dp[N][W]
```

**最后一步，把伪码翻译成代码，处理一些边界情况**。

我用 C++ 写的代码，把上面的思路完全翻译了一遍，并且处理了`w - wt[i-1]`可能小于 0 导致数组索引越界的问题：

```c++
int knapsack(int W, int N, vector<int>& wt, vector<int>& val) {
    // vector 全填入 0，base case 已初始化
    vector<vector<int>> dp(N + 1, vector<int>(W + 1, 0));
    for (int i = 1; i <= N; i++) {
        for (int w = 1; w <= W; w++) {
            if (w - wt[i-1] < 0) {
                // 当前背包容量装不下，只能选择不装入背包
                dp[i][w] = dp[i - 1][w];
            } else {
                // 装入或者不装入背包，择优
                dp[i][w] = max(dp[i - 1][w - wt[i-1]] + val[i-1], 
                               dp[i - 1][w]);
            }
        }
    }

    return dp[N][W];
}
```

### 2.3 动态规划玩游戏

- 魔塔游戏：174: 输入一个存储着整数的二维数组`grid`，

    - 如果`grid[i][j] > 0`，说明这个格子装着血瓶，经过它可以增加对应的生命值；
    - 如果`grid[i][j] == 0`，则这是一个空格子，经过它不会发生任何事情；
    - 如果`grid[i][j] < 0`，说明这个格子有怪物，经过它会损失对应的生命值。

    现在你是一名骑士，将会出现在最上角，公主被困在最右下角，你只能向右和向下移动，请问骑士的初始生命值至少为多少，才能成功救出公主？

    因为选择途径哪个中间节点，取决于该中间节点到终点所受的伤的大小，因此有以下dp数组定义：

    🌟 `dp[i][j]`表示从位置`(i,j)`**移动到终点**所需要的最小血量

    ```python
    """转移方程"""
    dp[i][j] = min(dp[i+1][j], dp[i][j+1]) - grid[i][j] # 先选一个掉血最少的路，最少需要的血量要减去ij位置的血量
    if dp[i][j] <= 0: dp[i][j] = 1 # 如果最少需要的血量小于等于0，此时意味着i，j位置有血瓶，那保证骑士此时有血即可，1
    ```

- 贪吃蛇游戏：1210. 穿过迷宫的最少移动次数

    <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230207222717460.png" alt="image-20230207222717460" style="zoom:50%;" />

- 掷骰子模拟：1223

    <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230210105003314.png" alt="image-20230210105003314" style="zoom:50%;" />



### 2.4 贪心算法 (时间规划问:435,452,1024,1326; 跳跃游戏:55,45,1326)

什么是贪心算法呢？贪心算法可以认为是动态规划算法的一个特例，相比动态规划，使用贪心算法需要满足更多的条件（贪心选择性质），但是效率比动态规划要高。

比如说一个算法问题使用暴力解法需要指数级时间，如果能使用动态规划消除重叠子问题，就可以降到多项式级别的时间，如果满足贪心选择性质，那么可以进一步降低时间复杂度，达到线性级别的。

什么是贪心选择性质呢，简单说就是：每一步都做出一个局部最优的选择，最终的结果就是全局最优。注意哦，这是一种特殊性质，其实只有一小部分问题拥有这个性质。

比如你面前放着 100 张人民币，你只能拿十张，怎么才能拿最多的面额？显然每次选择剩下钞票中面值最大的一张，最后你的选择一定是最优的。

然而，大部分问题都明显不具有贪心选择性质。比如打斗地主，对手出对儿三，按照贪心策略，你应该出尽可能小的牌刚好压制住对方，但现实情况我们甚至可能会出王炸。这种情况就不能用贪心算法，而得使用动态规划解决，参见前文 [动态规划解决博弈问题](http://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247484731&idx=4&sn=83fdd556c0d7c638b7a0df72129c0268&chksm=9bd7fb33aca07225f0528761352f1767de44debe2bf5210245f3d2d17b7ec23a70ef574505ef&scene=21#wechat_redirect)。





## 3. 题目

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

### 45. 跳跃游戏 II

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

### 53. 最大子数组和

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

### 62. 不同路径 <a name="different_route"> 📌</a>

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

### 63.不同路径 II

> 一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。
>
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish”）。
>
> 现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
>
> 网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。
>
> **示例 1：**
>
> <img src="https://assets.leetcode.com/uploads/2020/11/04/robot1.jpg" alt="img" style="zoom:50%;" />
>
> ```
> 输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
> 输出：2
> 解释：3x3 网格的正中间有一个障碍物。
> 从左上角到右下角一共有 2 条不同的路径：
> 1. 向右 -> 向右 -> 向下 -> 向下
> 2. 向下 -> 向下 -> 向右 -> 向右
> ```
>
> **示例 2：**
>
> <img src="https://assets.leetcode.com/uploads/2020/11/04/robot2.jpg" alt="img" style="zoom:50%;" />
>
> ```
> 输入：obstacleGrid = [[0,1],[0,0]]
> 输出：1
> ```
>
> **提示：**
>
> - `m == obstacleGrid.length`
> - `n == obstacleGrid[i].length`
> - `1 <= m, n <= 100`
> - `obstacleGrid[i][j]` 为 `0` 或 `1`

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        '''
        动态规划
        从finish grid[m][n] 往前倒推，
        if grid[i][j] == 0:
            if grid[i-1][j] == 0 and grid[i][j-1] == 0:
                dp[i][j] = dp[i-1][j] + dp[i][j-1]
            elif grid[i-1][j] == 0 and grid[i][j-1] != 0:
                dp[i][j] = dp[i-1][j]
            elif grid[i-1][j] != 0 and grid[i][j-1] == 0:
                dp[i][j] = dp[i][j-1]
            else:
                dp[i][j] = 0
        else:
            dp[i][j] = 0

        dp[0][0] = 1
        result: dp[m-1][n-1]
        '''
        grid = obstacleGrid
        m, n = len(grid), len(grid[0])
        
        # 处理边界
        if grid[0][0] == 1 or grid[m-1][n-1] == 1:
            return 0

        dp = [[0] * n for _ in range(m)]
        dp[0][0] = 1
        # 处理边界
        for i in range(1, m): # j = 0
            if grid[i][0] == 0:
                dp[i][0] = 1
            else:
                for ii in range(i, m):
                    dp[ii][0] = 0
                break
        for j in range(1, n): # i = 0
            if grid[0][j] == 0:
                dp[0][j] = 1
            else:
                for jj in range(j, n):
                    dp[0][jj] = 0
                break


        for i in range(1, m):
            for j in range(1, n):
                if  grid[i][j] == 0:
                    if grid[i-1][j] == 0 and grid[i][j-1] == 0:
                        dp[i][j] = dp[i-1][j] + dp[i][j-1]
                    elif grid[i-1][j] == 0 and grid[i][j-1] != 0:
                        dp[i][j] = dp[i-1][j]
                    elif grid[i-1][j] != 0 and grid[i][j-1] == 0:
                        dp[i][j] = dp[i][j-1]
                    else:
                        dp[i][j] = 0
                else:
                    dp[i][j] = 0
        return dp[m-1][n-1]
```



### 64. 最小路径和

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

### 70. 爬楼梯

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

### 78. 子集

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

### 121. 买卖股票的最佳时机

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

### 122. 买卖股票的最佳时机 II

> 给你一个整数数组 `prices` ，其中 `prices[i]` 表示某支股票第 `i` 天的价格。
>
> 在每一天，你可以决定是否购买和/或出售股票。你在任何时候 **最多** 只能持有 **一股** 股票。你也可以先购买，然后在 **同一天** 出售。
>
> 返回 *你能获得的 **最大** 利润* 。
>
>  
>
> **示例 1：**
>
> ```
> 输入：prices = [7,1,5,3,6,4]
> 输出：7
> 解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
>      随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3 。
>      总利润为 4 + 3 = 7 。
> ```
>
> **示例 2：**
>
> ```
> 输入：prices = [1,2,3,4,5]
> 输出：4
> 解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
>      总利润为 4 。
> ```
>
> **示例 3：**
>
> ```
> 输入：prices = [7,6,4,3,1]
> 输出：0
> 解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0 。
> ```
>
> **提示：**
>
> - `1 <= prices.length <= 3 * 10^4`
> - `0 <= prices[i] <= 10^4`

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        '''
        贪心：只能持有一只股票，等价于——选择不重合的多个区间[i, j)，使得 \sum (price[j] - price[i])最大化
        注意：price[j] - price[i] = 
            price[j] - price[j-1] + price[j-1] - price[j-2] + ... + price[i+1] - price[i]
            因此问题转化为，求多个长度为1的区间，这些区间的总和加起来最大
            容易得，当所有区间之差大于0的时候，总和最大
        '''
        n = len(prices)
        res = 0
        if n == 1:
            return 0
        for i in range(1, n):
            if prices[i] - prices[i-1] > 0:
                res += prices[i] - prices[i-1]
        return res

```

### 123. 买卖股票的最佳时机 III

> 给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 **两笔** 交易。
>
> **注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> **示例 1:**
>
> ```
> 输入：prices = [3,3,5,0,0,3,1,4]
> 输出：6
> 解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
>      随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。
> ```
>
> **示例 2：**
>
> ```
> 输入：prices = [1,2,3,4,5]
> 输出：4
> 解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
>      注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
>      因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
> ```
>
> **示例 3：**
>
> ```
> 输入：prices = [7,6,4,3,1] 
> 输出：0 
> 解释：在这个情况下, 没有交易完成, 所以最大利润为 0。
> ```
>
> **示例 4：**
>
> ```
> 输入：prices = [1]
> 输出：0
> ```
>
> **提示：**
>
> - `1 <= prices.length <= 105`
> - `0 <= prices[i] <= 105`

❗️官解

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        '''
        buy1: 在该天第一次买入股票可获得的最大收益 
        sell1: 在该天第一次卖出股票可获得的最大收益
        buy2: 在该天第二次买入股票可获得的最大收益
        sell2: 在该天第二次卖出股票可获得的最大收益
        '''
        n = len(prices)
        buy1 = buy2 = -prices[0] # 第一天不管怎么买都是亏
        sell1 = sell2 = 0 # 第一天再售出，则收益为0
        for i in range(1, n):
            buy1 = max(buy1, -prices[i])
            sell1 = max(sell1, buy1 + prices[i])
            buy2 = max(buy2, sell1 - prices[i])
            sell2 = max(sell2, buy2 + prices[i])
        return sell2
```



⚠️：动态规划但超时版

```python 
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        '''
        动态规划
        dp[i][j]: 在第i天结束后，呈现j状态，此时的最大收益是多少
        j = 0: 买入一次, dp[i][0] = - prices[i]
        j = 1: 买卖一次, dp[i][1] = prices[i] - min(prices[0:i]), 以i天之前最小买入价入手
        j = 2: 买卖一次 + 买入一次, dp[i][2] = max(dp[0:i][1]) - prices[i], 以前i天最大买卖利润为基础
        j = 3: 买卖两次, dp[i][3] = max(dp[0:k][1]) + prices[i] - min(prices[j:i]), j = argmax dp[0:k][1]
        j = 4: 什么都没做, dp[i][4] = 0
        return max(dp[n][:])
        
        base case: i = 0: 第0天不管case1，2，3，4做了多少操作，收益都是0
        '''
        n = len(prices)
        dp = [[0] * 5 for _ in range(n)]
        res = 0
        for i in range(n):
            # 第i天
            dp[i][0] = - prices[i]
            if i > 0:
                dp[i][1] = prices[i] - min(prices[0:i])
            if i > 0:
                dp[i][2] = max([dp[k][1] for k in range(i)]) - prices[i]
            if i > 0:
                # 此时不确定是在哪天完成的第一笔买卖；设第一笔完成的时间为k，k < i
                for k in range(i): # ！！！这一步超时了
                    dp[i][3] = max([dp[i][3], dp[k][1] + prices[i] - min(prices[k:i])])
            dp[i][4] = 0
            res = max([res, dp[i][1], dp[i][3], dp[i][4]]) # 没有任何买卖，或买卖完成一次或两次 
        return res
```





### 139. 单词拆分 <a name="split_words">📌</a>

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

### 198. 打家劫舍 <a name="rob">📌</a>

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

### 413. 等差数列划分

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

### 542. 01 矩阵

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

### 221. 最大正方形 <a name="max_squre">📌</a>

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

### 279. 完全平方数

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

<img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230110231240723.png" alt="image-20230110231240723" style="zoom:50%;" />

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

### 91. 解码方法

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

### 300. 最长递增子序列

> 给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。
>
> **子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。
>
> **示例 1：**
>
> ```
> 输入：nums = [10,9,2,5,3,7,101,18]
> 输出：4
> 解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [0,1,0,3,2,3]
> 输出：4
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [7,7,7,7,7,7,7]
> 输出：1 
> ```
>
> **提示：**
>
> - `1 <= nums.length <= 2500`
> - `-10^4 <= nums[i] <= 10^4` 
>
> **进阶：**
>
> - 你能将算法的时间复杂度降低到 `O(n log(n))` 吗?

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        '''
        动态规划：dp[i]表示以nums[i]结尾的最长的严格递增子序列长度
        如果在nums[i]前有一个小于nums[i]的数字nums[j], 则
        dp[i] = max([dp[j] + 1, dp[i]]) # 即记录所有dp[j]+1的最大值

        即：在i前查找比nums[i]还小的数字
        '''
        n = len(nums)
        dp = [1] * n
        for i in range(n):
            for j in range(i):
                if nums[j] < nums[i]:
                    dp[i] = max([dp[j] + 1, dp[i]])
        return max(dp)

        

```



### 1143. 最长公共子序列

> 给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。
>
> 一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
>
> - 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。
>
> 两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。
>
> **示例 1：**
>
> ```
> 输入：text1 = "abcde", text2 = "ace" 
> 输出：3  
> 解释：最长公共子序列是 "ace" ，它的长度为 3 。
> ```
>
> **示例 2：**
>
> ```
> 输入：text1 = "abc", text2 = "abc"
> 输出：3
> 解释：最长公共子序列是 "abc" ，它的长度为 3 。
> ```
>
> **示例 3：**
>
> ```
> 输入：text1 = "abc", text2 = "def"
> 输出：0
> 解释：两个字符串没有公共子序列，返回 0 。
> ```
>
>  **提示：**
>
> - `1 <= text1.length, text2.length <= 1000`
> - `text1` 和 `text2` 仅由小写英文字符组成。

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        '''
        动态规划：可以建立一个二维数组dp，其中dp[i][j] 表示到第一个字符串位置i 为止、到
        第二个字符串位置j 为止、最长的公共子序列长度
        
        如果text1[i] == text[j]，则这个位置是新的公共子序列的末尾，则子序列长度+1
        否则，这个位置继承上一个位置的长度，即max([dp[i-1][j], dp[i][j-1]])
        '''
        m = len(text1)
        n = len(text2)
        dp = [[0] * (n+1) for _ in range(m+1)]
        for i in range(1, m+1):
            for j in range(1, n+1):
                if text1[i-1] == text2[j-1]:
                    dp[i][j] = dp[i-1][j-1] + 1
                else:
                    dp[i][j] = max([dp[i-1][j], dp[i][j-1]])
        return dp[-1][-1]
```

### 416. 分割等和子集

> 给你一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。
>
> **示例 1：**
>
> ```
> 输入：nums = [1,5,11,5]
> 输出：true
> 解释：数组可以分割成 [1, 5, 5] 和 [11] 。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [1,2,3,5]
> 输出：false
> 解释：数组不能分割成两个元素和相等的子集。
> ```
>
> **提示：**
>
> - `1 <= nums.length <= 200`
> - `1 <= nums[i] <= 100`

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        '''
        一个0-1背包问题，在数组中选择一部分数字，使得他的和为sum(nums) - sum(nums - selected set)
        '''
        # 1. 状态：数字nums[i]是否被选择、此时的和为多少
        # 2. dp数组的定义：dp[i][j]——在遍历到第i个数字的时候，此时的和为j，这个状态是否是达的
        # 3. 转移方程：对于一个数字nums[i]，
        #       如果加入这个数字，dp[i][j] = dp[i-1][j-nums[i]]
        #       如果不加入这个数字，dp[i][j] = dp[i-1][j]
        #       综上：dp[i][j] = dp[i-1][j-nums[i]] or dp[i-1][j]，两个方案有一个可达即可

        
        target = sum(nums) / 2 # 背包内的数字和只能是一半
        # 边界：如果target为小数，则不可分割
        if int(target) != target:
            return False
        target = int(target)
        n = len(nums)
        dp = [[False] * (target + 1) for _ in range(n+1)] # 初始化所有都是不可达的
        dp[0][0] = True # 
        # 套公式：
        for i in range(1, n+1):
            # 遍历nums[i-1]
            for j in range(target + 1):
                if j - nums[i-1] >= 0:
                    dp[i][j] = dp[i-1][j-nums[i-1]] or dp[i-1][j]
                else:
                    # 如果加入这个数字会使得总和直接超过j
                    dp[i][j] = dp[i-1][j]
        return dp[n][target]
```

### 474. 一和零

> 给你一个二进制字符串数组 `strs` 和两个整数 `m` 和 `n` 。
>
> 请你找出并返回 `strs` 的最大子集的长度，该子集中 **最多** 有 `m` 个 `0` 和 `n` 个 `1` 。
>
> 如果 `x` 的所有元素也是 `y` 的元素，集合 `x` 是集合 `y` 的 **子集** 。
>
> **示例 1：**
>
> ```
> 输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
> 输出：4
> 解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。
> 其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。
> ```
>
> **示例 2：**
>
> ```
> 输入：strs = ["10", "0", "1"], m = 1, n = 1
> 输出：2
> 解释：最大的子集是 {"0", "1"} ，所以答案是 2 。
> ```
>
> **提示：**
>
> - `1 <= strs.length <= 600`
> - `1 <= strs[i].length <= 100`
> - `strs[i]` 仅由 `'0'` 和 `'1'` 组成
> - `1 <= m, n <= 100`

```python
class Solution:
    def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
        def count(s):
            num0, num1 = 0, 0
            for i in list(s):
                if i == '0':
                    num0 += 1
                else:
                    num1 += 1
            return num0, num1

        '''
        背包问题：选几个子串能够满足0/1要求
        '''
        # 1. 状态，遍历到第几个元素，有几个0，有几个1
        # 2. dp的定义：dp[i][mm][nn]: 在遍历到第strs[i]时，有mm个0，nn个1，此时的元素最多有几个
        # 3. 转移方程
        #       如果选择strs[i], dp[i][mm][nn] = dp[i-1][mm-m'][nn-n'] + 1
        #       如果不选择strs[i], dp[i][mm][nn] = dp[i-1][mm][nn]
        #       综上，dp[i][mm][nn] = max([dp[i-1][mm-m'][nn-n'] + 1, dp[i-1][mm][nn]])
        # 初始：dp[0][0][0] = 0
        l = len(strs)
        dp = [[[0] * (n+1) for _ in range(m+1)] for _ in range(l+1)]
        for i in range(1, l+1):
            # strs[i-1]
            num0, num1 = count(strs[i-1])
            for mm in range(0, m+1): # 要遍历每个状态，所以从0开始
                for nn in range(0, n+1):
                    if mm-num0 >= 0 and nn-num1 >= 0:
                        dp[i][mm][nn] = max([dp[i-1][mm-num0][nn-num1] + 1, dp[i-1][mm][nn]])
                    else:
                        dp[i][mm][nn] = dp[i-1][mm][nn]
        return dp[l][m][n]
```

### 518. 零钱兑换 II

> 给你一个整数数组 `coins` 表示不同面额的硬币，另给一个整数 `amount` 表示总金额。
>
> 请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 `0` 。
>
> 假设每一种面额的硬币有无限个。 
>
> 题目数据保证结果符合 32 位带符号整数。
>
> **示例 1：**
>
> ```
> 输入：amount = 5, coins = [1, 2, 5]
> 输出：4
> 解释：有四种方式可以凑成总金额：
> 5=5
> 5=2+2+1
> 5=2+1+1+1
> 5=1+1+1+1+1
> ```
>
> **示例 2：**
>
> ```
> 输入：amount = 3, coins = [2]
> 输出：0
> 解释：只用面额 2 的硬币不能凑成总金额 3 。
> ```
>
> **示例 3：**
>
> ```
> 输入：amount = 10, coins = [10] 
> 输出：1
> ```
>
> **提示：**
>
> - `1 <= coins.length <= 300`
> - `1 <= coins[i] <= 5000`
> - `coins` 中的所有值 **互不相同**
> - `0 <= amount <= 5000`

```python
class Solution:
    def change(self, amount: int, coins: List[int]) -> int:
        '''
        动态规划 - 完全背包
        1. 状态：硬币i，金额j，dp[i][j]: 使用第i-1个硬币的时候凑出的组合数
        2. 选择：是否装进背包
        3. 转移：
            使用i-1：dp[i][j] = dp[i][j-coins[i-1]] 这里和0-1不同
            不使用i-1：dp[i][j] = dp[i-1][j]
            dp[i][j] = dp[i-1][j-coins[i-1]] + dp[i-1][j]
        '''
        n = len(coins)
        dp = [[0]*(amount+1) for _ in range(n+1)]
        # dp[0][j] = 0
        # dp[i][0] = 1
        for i in range(0, n+1):
            dp[i][0] = 1

        for i in range(1, n+1):
            for j in range(1, amount+1):
                if j - coins[i-1] >= 0:
                    dp[i][j] = dp[i][j-coins[i-1]] + dp[i-1][j]
                else:
                    dp[i][j] = dp[i-1][j]
        return dp[n][amount]
```

### 322. 零钱兑换

> 给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。
>
> 计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。
>
> 你可以认为每种硬币的数量是无限的。
>
> **示例 1：**
>
> ```
> 输入：coins = [1, 2, 5], amount = 11
> 输出：3 
> 解释：11 = 5 + 5 + 1
> ```
>
> **示例 2：**
>
> ```
> 输入：coins = [2], amount = 3
> 输出：-1
> ```
>
> **示例 3：**
>
> ```
> 输入：coins = [1], amount = 0
> 输出：0
> ```
>
> **提示：**
>
> - `1 <= coins.length <= 12`
> - `1 <= coins[i] <= 231 - 1`
> - `0 <= amount <= 104`

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        '''
        动态规划：背包 
        dp[i][j]: 遍历到coins[i-1]，总金额为j时，所需的最少硬币个数
        - 如果使用coins[i-1]: dp[i][j] = dp[i][j-coins[i-1]] + 1
        - 如果不使用: dp[i][j] = dp[i-1][j]
        dp[i][j] = min([dp[i][j-coins[i-1]], dp[i-1][j]])
        '''
        n = len(coins)
        dp = [[amount+1]*(amount+1) for _ in range(n+1)]
        # dp[i][0] = 0
        # dp[0][j] = -1
        # dp[0][0] = 0
        dp[0][0] = 0
        for i in range(0, n+1):
            dp[i][0] = 0 # 要凑出0元，需要0个

        for i in range(1, n+1):
            for j in range(1, amount+1):
                if j-coins[i-1] >= 0:
                    dp[i][j] = min([dp[i][j-coins[i-1]]+1, dp[i-1][j]])
                else:
                    dp[i][j] = dp[i-1][j]
        return -1 if dp[n][amount] == amount+1 else dp[n][amount]
      '''
      这里注意，我们把dp 数组初始化为amount + 1 而不是-1 的原因是，在动态规划过程中有求
最小值的操作，如果初始化成-1 则会导致结果始终为-1。至于为什么取这个值，是因为i 最大可
以取amount，而最多的组成方式是只用1 元硬币，因此amount + 1 一定大于所有可能的组合方
式，取最小值时一定不会是它。在动态规划完成后，若结果仍然是此值，则说明不存在满足条件
的组合方法，返回-1。
      '''
```

### 72. 编辑距离

> 给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数* 。
>
> 你可以对一个单词进行如下三种操作：
>
> - 插入一个字符
> - 删除一个字符
> - 替换一个字符 
>
> **示例 1：**
>
> ```
> 输入：word1 = "horse", word2 = "ros"
> 输出：3
> 解释：
> horse -> rorse (将 'h' 替换为 'r')
> rorse -> rose (删除 'r')
> rose -> ros (删除 'e')
> ```
>
> **示例 2：**
>
> ```
> 输入：word1 = "intention", word2 = "execution"
> 输出：5
> 解释：
> intention -> inention (删除 't')
> inention -> enention (将 'i' 替换为 'e')
> enention -> exention (将 'n' 替换为 'x')
> exention -> exection (将 'n' 替换为 'c')
> exection -> execution (插入 'u')
> ```
>
> **提示：**
>
> - `0 <= word1.length, word2.length <= 500`
> - `word1` 和 `word2` 由小写英文字母组成

```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        '''
        编辑距离：编辑距离是对称的
        '''
        # 按照套路来写
        # 动态规划
        m = len(word1)
        n = len(word2)
        if m == 0: return n
        if n == 0: return m
        dp = [[0] * (n+1) for _ in range(m+1)]
        for i in range(m+1):
            dp[i][0] = i # 边界，当一个指针为空，则编辑距离就是另一个字符串的长度
        for j in range(n+1):
            dp[0][j] = j 

        # 遍历dp table
        for i in range(1, m+1):
            for j in range(1, n+1):
                # word1[i-1], word2[j-1]
                if word1[i-1] == word2[j-1]:
                    # do nothing 
                    dp[i][j] = dp[i-1][j-1]
                else:
                    dp[i][j] = min([
                        dp[i][j-1] + 1, # i指针位置增加一个与j指针相同的字母
                        dp[i-1][j] + 1, # 删去i指针
                        dp[i-1][j-1] + 1 # i指针改为j指针内容
                    ])
        return dp[m][n]
```

### 5. 最长回文子串

> 给你一个字符串 `s`，找到 `s` 中最长的回文子串。
>
> 如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。 
>
> **示例 1：**
>
> ```
> 输入：s = "babad"
> 输出："bab"
> 解释："aba" 同样是符合题意的答案。
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "cbbd"
> 输出："bb"
> ```
>
> **提示：**
>
> - `1 <= s.length <= 1000`
> - `s` 仅由数字和英文字母组成

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        # 动态规划一下
        '''
        dp[i][j]: s[i]到s[j]是否为回文
        if dp[i+1][j-1] is True and s[i] == s[j]: dp[i][j] = True; i<=j
        dp[i][i] = True

        注意：在状态转移方程中，我们是从长度较短的字符串向长度较长的字符串进行转移的，因此一定要注意动态规划的循环顺序。
        '''
        n = len(s)
        dp = [[False] * n for _ in range(n)]
        max_len = 0
        max_idx = (0,0)
        for i in range(n):
            dp[i][i] = True
        # 由于要从长度较短的字符串向长度最长的字符串转移，这里考虑枚举字符串的长度
        for l in range(2, n+1):
            # 枚举字符串的起始位置：
            for i in range(0, n):
                j = i + l - 1
                if j >= n:
                    break
                # 每次只要比较s[i]是否等于s[j]
                if s[i] == s[j]:
                    if j-i+1 <= 2:
                        dp[i][j] = True # 长度为2的回文
                    else:
                        dp[i][j] = dp[i+1][j-1] # 长度超过3，则由更短的子串决定
                    if dp[i][j] is True and j-i+1 > max_len:
                        max_len = j-i+1
                        max_idx = (i, j)
                else:
                    dp[i][j] = False
        return s[max_idx[0]: max_idx[1]+1]
```

### 516. 最长回文子序列

> 给你一个字符串 `s` ，找出其中最长的回文子序列，并返回该序列的长度。
>
> 子序列定义为：不改变剩余字符顺序的情况下，删除某些字符或者不删除任何字符形成的一个序列。
>
> **示例 1：**
>
> ```
> 输入：s = "bbbab"
> 输出：4
> 解释：一个可能的最长回文子序列为 "bbbb" 。
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "cbbd"
> 输出：2
> 解释：一个可能的最长回文子序列为 "bb" 。
> ```
>
> **提示：**
>
> - `1 <= s.length <= 1000`
> - `s` 仅由小写英文字母组成

```python
class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        '''
        动态规划自己写一下试试：
        dp[i][j]: s的下标范围i-j内，最长回文子序列的长度，i <= j
        base case: dp[i][i] = 1
        状态转移：
            如果s[i] == s[j]，则他们有资格成为新子串的首尾，dp[i][j] = dp[i+1][j-1] + 2
            s[i] != s[j]，则他们不能成为新子串的首尾，dp[i][j] = max([dp[i+1][j], dp[i][j-1]])      
            根据转移方程，i要从大到小遍历，j要送小到大遍历      
        '''
        n = len(s)
        dp = [[0] * n for _ in range(n)]
        for i in range(n):
            dp[i][i] = 1
        
        for i in range(n, -1, -1):
            for j in range(i+1, n):
                if s[i] == s[j]:
                    dp[i][j] = dp[i+1][j-1] + 2
                else:
                    dp[i][j] = max([dp[i+1][j], dp[i][j-1]])
        return dp[0][n-1]
```

### 1312. 让字符串成为回文串的最少插入次数

> 给你一个字符串 `s` ，每一次操作你都可以在字符串的任意位置插入任意字符。
>
> 请你返回让 `s` 成为回文串的 **最少操作次数** 。
>
> 「回文串」是正读和反读都相同的字符串。
>
> **示例 1：**
>
> ```
> 输入：s = "zzazz"
> 输出：0
> 解释：字符串 "zzazz" 已经是回文串了，所以不需要做任何插入操作。
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "mbadm"
> 输出：2
> 解释：字符串可变为 "mbdadbm" 或者 "mdbabdm" 。
> ```
>
> **示例 3：**
>
> ```
> 输入：s = "leetcode"
> 输出：5
> 解释：插入 5 个字符后字符串变为 "leetcodocteel" 。
> ```
>
> **提示：**
>
> - `1 <= s.length <= 500`
> - `s` 中所有字符都是小写字母。

```python
class Solution:
    def minInsertions(self, s: str) -> int:
        '''
        动态规划
        dp[i][j]表示使得s[i,...,j]成为回文串的最少插入次数
        i <= j
        base case: i == j: dp[i][i] = 0
        状态转移方程：
            如果s[i] == s[j]，dp[i][j] = dp[i+1][j-1]
            否则，需要一次操作使得s[i] = s[j], dp[i][j] = min([dp[i+1][j], dp[i][j-1]]) + 1 (把s[i+1,...,j]和s[i,...j-1]变成回文串，然后找编辑次数最少的)
        '''

        n = len(s)
        dp = [[0] * n for _ in range(n)]
        for i in range(n-1, -1, -1):
            for j in range(i+1, n):
                if s[i] == s[j]:
                    dp[i][j] = dp[i+1][j-1]
                else:
                    dp[i][j] = min([dp[i+1][j], dp[i][j-1]]) + 1
        return dp[0][n-1]
```





### 435. 无重叠区间

> 给定一个区间的集合 `intervals` ，其中 `intervals[i] = [starti, endi]` 。返回 *需要移除区间的最小数量，使剩余区间互不重叠* 。
>
> **示例 1:**
>
> ```
> 输入: intervals = [[1,2],[2,3],[3,4],[1,3]]
> 输出: 1
> 解释: 移除 [1,3] 后，剩下的区间没有重叠。
> ```
>
> **示例 2:**
>
> ```
> 输入: intervals = [ [1,2], [1,2], [1,2] ]
> 输出: 2
> 解释: 你需要移除两个 [1,2] 来使剩下的区间没有重叠。
> ```
>
> **示例 3:**
>
> ```
> 输入: intervals = [ [1,2], [2,3] ]
> 输出: 0
> 解释: 你不需要移除任何区间，因为它们已经是无重叠的了。
> ```
>
> **提示:**
>
> - `1 <= intervals.length <= 105`
> - `intervals[i].length == 2`
> - `-5 * 10^4 <= starti < endi <= 5 * 10^4`

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        '''
        问题转化为，选取最多不重合的区间，贪心：每次选取结束的最早的区间
        1. 从区间集合 intvs 中选择一个区间 x，这个 x 是在当前所有区间中结束最早的（end 最小）。
        2. 把所有与 x 区间相交的区间从区间集合 intvs 中删除。
        3. 重复步骤 1 和 2，直到 intvs 为空为止。之前选出的那些 x 就是最大不相交子集。
        '''
        intervals.sort(key=lambda x: x[1]) # 按照结束时间做排序
        cur_end = intervals[0][1]
        n = len(intervals)
        res = 1 # 最少有一个
        for i in range(1, n):
            if intervals[i][0] >= cur_end:
                cur_end = intervals[i][1]
                res += 1
        
        return n - res

```

### 452. 用最少数量的箭引爆气球

> 有一些球形气球贴在一堵用 XY 平面表示的墙面上。墙面上的气球记录在整数数组 `points` ，其中`points[i] = [xstart, xend]` 表示水平直径在 `xstart` 和 `xend`之间的气球。你不知道气球的确切 y 坐标。
>
> 一支弓箭可以沿着 x 轴从不同点 **完全垂直** 地射出。在坐标 `x` 处射出一支箭，若有一个气球的直径的开始和结束坐标为 `x``start`，`x``end`， 且满足  `xstart ≤ x ≤ x``end`，则该气球会被 **引爆** 。可以射出的弓箭的数量 **没有限制** 。 弓箭一旦被射出之后，可以无限地前进。
>
> 给你一个数组 `points` ，*返回引爆所有气球所必须射出的 **最小** 弓箭数* 。
>
> **示例 1：**
>
> ```
> 输入：points = [[10,16],[2,8],[1,6],[7,12]]
> 输出：2
> 解释：气球可以用2支箭来爆破:
> -在x = 6处射出箭，击破气球[2,8]和[1,6]。
> -在x = 11处发射箭，击破气球[10,16]和[7,12]。
> ```
>
> **示例 2：**
>
> ```
> 输入：points = [[1,2],[3,4],[5,6],[7,8]]
> 输出：4
> 解释：每个气球需要射出一支箭，总共需要4支箭。
> ```
>
> **示例 3：**
>
> ```
> 输入：points = [[1,2],[2,3],[3,4],[4,5]]
> 输出：2
> 解释：气球可以用2支箭来爆破:
> - 在x = 2处发射箭，击破气球[1,2]和[2,3]。
> - 在x = 4处射出箭，击破气球[3,4]和[4,5]。
> ```
>
> **提示:**
>
> - `1 <= points.length <= 10^5`
> - `points[i].length == 2`
> - `-2^31 <= xstart < xend <= 2^31 - 1`

```python
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        '''
        贪心思路：将所有区间按结束坐标做排列
        1. 先假设在恰当的位置打穿最左边的气球
        2. 排除掉所有和最左侧气球重合的气球
        3. 重复第一步
        '''
        points.sort(key=lambda x: x[-1])
        res = 1 # 最少一支箭
        cur_end = points[0][-1]
        n = len(points)
        for i in range(1, n):
            if points[i][0] > cur_end: # 要不擦边才算没有射破
                cur_end = points[i][-1]
                res += 1
        return res
```

### 1024. 视频拼接

> 你将会获得一系列视频片段，这些片段来自于一项持续时长为 `time` 秒的体育赛事。这些片段可能有所重叠，也可能长度不一。
>
> 使用数组 `clips` 描述所有的视频片段，其中 `clips[i] = [starti, endi]` 表示：某个视频片段开始于 `starti` 并于 `endi` 结束。
>
> 甚至可以对这些片段自由地再剪辑：
>
> - 例如，片段 `[0, 7]` 可以剪切成 `[0, 1] + [1, 3] + [3, 7]` 三部分。
>
> 我们需要将这些片段进行再剪辑，并将剪辑后的内容拼接成覆盖整个运动过程的片段（`[0, time]`）。返回所需片段的最小数目，如果无法完成该任务，则返回 `-1` 。
>
> **示例 1：**
>
> ```
> 输入：clips = [[0,2],[4,6],[8,10],[1,9],[1,5],[5,9]], time = 10
> 输出：3
> 解释：
> 选中 [0,2], [8,10], [1,9] 这三个片段。
> 然后，按下面的方案重制比赛片段：
> 将 [1,9] 再剪辑为 [1,2] + [2,8] + [8,9] 。
> 现在手上的片段为 [0,2] + [2,8] + [8,10]，而这些覆盖了整场比赛 [0, 10]。
> ```
>
> **示例 2：**
>
> ```
> 输入：clips = [[0,1],[1,2]], time = 5
> 输出：-1
> 解释：
> 无法只用 [0,1] 和 [1,2] 覆盖 [0,5] 的整个过程。
> ```
>
> **示例 3：**
>
> ```
> 输入：clips = [[0,1],[6,8],[0,2],[5,6],[0,4],[0,3],[6,7],[1,3],[4,7],[1,4],[2,5],[2,6],[3,4],[4,5],[5,7],[6,9]], time = 9
> 输出：3
> 解释： 
> 选取片段 [0,4], [4,7] 和 [6,9] 。
> ```
>
>  
>
> **提示：**
>
> - `1 <= clips.length <= 100`
> - `0 <= starti <= endi <= 100`
> - `1 <= time <= 100`

```python
class Solution:
    def videoStitching(self, clips: List[List[int]], time: int) -> int:
        '''
        视频按照开始时间的升序做排序，开始时间相同的按结束时间降序排序
        1. 选最先开始的片段
        2. 开始时间早于或等于当前结束时间，并最晚结束的片段
        3. 重复2直到覆盖所有片段
        '''
        # 排序，按开始时间归类
        sorted_clips = {} # key: start time, value: list
        for start, end in clips:
            if sorted_clips.get(start) is None:
                sorted_clips[start] = []
            sorted_clips[start].append([start, end])
        # 同时开始的片段，按结束时间降序排列
        for k, v in sorted_clips.items():
            sorted_clips[k].sort(key=lambda x:x[-1], reverse=True)
        # 按开始时间升序排列
        ks = list(sorted_clips.keys())
        ks.sort()
        new_clips = []
        for k in ks:
            new_clips += sorted_clips[k]
        
        if new_clips[0][0] > 0:
            return -1
        n = len(new_clips)
        cur_start, cur_end = new_clips[0][0], new_clips[0][-1]
        res = 1
        sel_idx = 0

        while cur_end < time and sel_idx < n:
            # 选sel_idx+1:n中最晚结束的
            max_end = -1
            tmp = -1 # 最晚结束的那个项目的序号
            for j in range(sel_idx+1, n):
                if new_clips[j][0] <= cur_end and new_clips[j][-1] > cur_end and new_clips[j][-1] > max_end:
                    max_end = new_clips[j][-1]
                    tmp = j
            if tmp == -1: return -1 # 选不出能够接上已有视频的片段
            cur_end = new_clips[tmp][-1]
            sel_idx = tmp
            res += 1
        return res
```

### 1326. 灌溉花园的最少水龙头数目

> 在 x 轴上有一个一维的花园。花园长度为 `n`，从点 `0` 开始，到点 `n` 结束。
>
> 花园里总共有 `n + 1` 个水龙头，分别位于 `[0, 1, ..., n]` 。
>
> 给你一个整数 `n` 和一个长度为 `n + 1` 的整数数组 `ranges` ，其中 `ranges[i]` （下标从 0 开始）表示：如果打开点 `i` 处的水龙头，可以灌溉的区域为 `[i - ranges[i], i + ranges[i]]` 。
>
> 请你返回可以灌溉整个花园的 **最少水龙头数目** 。如果花园始终存在无法灌溉到的地方，请你返回 **-1** 。
>
>  
>
> **示例 1：**
>
> <img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/19/1685_example_1.png" alt="img" style="zoom:50%;" />
>
> ```
> 输入：n = 5, ranges = [3,4,1,1,0,0]
> 输出：1
> 解释：
> 点 0 处的水龙头可以灌溉区间 [-3,3]
> 点 1 处的水龙头可以灌溉区间 [-3,5]
> 点 2 处的水龙头可以灌溉区间 [1,3]
> 点 3 处的水龙头可以灌溉区间 [2,4]
> 点 4 处的水龙头可以灌溉区间 [4,4]
> 点 5 处的水龙头可以灌溉区间 [5,5]
> 只需要打开点 1 处的水龙头即可灌溉整个花园 [0,5] 。
> ```
>
> **示例 2：**
>
> ```
> 输入：n = 3, ranges = [0,0,0,0]
> 输出：-1
> 解释：即使打开所有水龙头，你也无法灌溉整个花园。
> ```
>
>  
>
> **提示：**
>
> - `1 <= n <= 104`
> - `ranges.length == n + 1`
> - `0 <= ranges[i] <= 100`

```python
class Solution:
    def minTaps(self, n: int, ranges: List[int]) -> int:
        '''
        感觉是动态规划，或者是贪心，类似于视频拼接（每一步都寻找当当前能够覆盖的最远的水龙头）
        1. 每个水龙头按照能覆盖的范围的起始点开始升序排列，相同时间开始的，按照结束时间倒序排序
        2. 选最先开始的片段
        3. 开始时间早于或等于当前结束时间，并最晚结束的片段
        4. 重复3直到覆盖所有片段

        本题与视频拼接的区别在于：最早开始的水龙头不一定要被选中，只需覆盖0即可
        '''
        range_list = []
        for i, r in enumerate(ranges):
            if i-r < 0:
                range_list.append((0, i+r))
            else:
                range_list.append((i-r, i+r))
        tmp = {}
        for item in range_list:
            if tmp.get(item[0]) is None:
                tmp[item[0]] = []
            tmp[item[0]].append(item)
        # 倒序
        for k, v in tmp.items():
            tmp[k].sort(key=lambda x:x[-1], reverse=True)
        range_list = []
        kl = list(tmp.keys())
        kl.sort()
        # 正序
        for k in kl:
            range_list += tmp[k]
        # 
        use = 1
        idx = 0
        start = range_list[idx][0]
        end = range_list[idx][1] # 当前覆盖到的最后区域
        if start > 0:
            return -1 # 最早开始的都不能覆盖到0
        while end < n and idx < len(range_list): # 没有覆盖到最后
            maxx = -1 # 选能够得到的最远覆盖
            sel_idx = -1 # 下一次要选择的位置
            for select in range(idx+1, len(range_list)):
                if range_list[select][0] <= end and range_list[select][1] > end and range_list[select][1] > maxx: # 能和上一段连起来，且能覆盖最远的水龙头
                    maxx = range_list[select][1]
                    sel_idx = select
            if sel_idx == -1: # 找不到符合标准的下一段
                return -1
            else:
                idx = sel_idx
                end = range_list[idx][1]
                use += 1
        return use
```



### 174. 地下城游戏

> 一些恶魔抓住了公主（**P**）并将她关在了地下城的右下角。地下城是由 M x N 个房间组成的二维网格。我们英勇的骑士（**K**）最初被安置在左上角的房间里，他必须穿过地下城并通过对抗恶魔来拯救公主。
>
> 骑士的初始健康点数为一个正整数。如果他的健康点数在某一时刻降至 0 或以下，他会立即死亡。
>
> 有些房间由恶魔守卫，因此骑士在进入这些房间时会失去健康点数（若房间里的值为*负整数*，则表示骑士将损失健康点数）；其他房间要么是空的（房间里的值为 *0*），要么包含增加骑士健康点数的魔法球（若房间里的值为*正整数*，则表示骑士将增加健康点数）。
>
> 为了尽快到达公主，骑士决定每次只向右或向下移动一步。
>
>  
>
> **编写一个函数来计算确保骑士能够拯救到公主所需的最低初始健康点数。**
>
> 例如，考虑到如下布局的地下城，如果骑士遵循最佳路径 `右 -> 右 -> 下 -> 下`，则骑士的初始健康点数至少为 **7**。
>
> <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230207102954229.png" alt="image-20230207102954229" style="zoom:50%;" />
>
> **说明:**
>
> - 骑士的健康点数没有上限。
> - 任何房间都可能对骑士的健康点数造成威胁，也可能增加骑士的健康点数，包括骑士进入的左上角房间以及公主被监禁的右下角房间。

```python
class Solution:
    def calculateMinimumHP(self, dungeon: List[List[int]]) -> int:
        '''
        动态规划：要最小化骑士的生命点数，意味着要最小化骑士在路上受的伤。
        *而不是最大化路上的血瓶，因为很容易观察到：在一个无伤的路径上，骑士的最小初始血量是1，而有伤但血瓶更多的路径上，要优先保障骑士挺过第一个伤*

        因为选择途径哪个中间节点，取决于该中间节点到终点所受的伤的大小，因此有以下dp数组定义
        * dp[i][j]表示从位置(i,j)移动到终点所需要的最小血量
        
        转移方程：

        dp[i][j] = min(dp[i+1][j], dp[i][j+1]) - grid[i][j] # 先选一个掉血最少的路，最少需要的血量要减去ij位置的血量
        if dp[i][j] <= 0: dp[i][j] = 1 # 如果最少需要的血量小于等于0，此时意味着i，j位置有血瓶，那保证骑士此时有血即可，1
        '''
        m, n = len(dungeon), len(dungeon[0])
        dp = [[0] * n for _ in range(m)]
        for i in range(m-1, -1, -1):
            for j in range(n-1, -1, -1):
                # base case 1
                if i == m-1 and j == n-1:
                    dp[i][j] = 1 - dungeon[i][j]
                # base case 2
                else:
                    if i == m-1:
                        dp[i][j] = dp[i][j+1] - dungeon[i][j]
                    elif j == n-1:
                        dp[i][j] = dp[i+1][j] - dungeon[i][j]
                    else:
                        dp[i][j] = min([dp[i+1][j], dp[i][j+1]]) - dungeon[i][j]
                if dp[i][j] <= 0:
                    dp[i][j] = 1
        return dp[0][0]
```

### 1210. 穿过迷宫的最少移动次数

> 你还记得那条风靡全球的贪吃蛇吗？
>
> 我们在一个 `n*n` 的网格上构建了新的迷宫地图，蛇的长度为 2，也就是说它会占去两个单元格。蛇会从左上角（`(0, 0)` 和 `(0, 1)`）开始移动。我们用 `0` 表示空单元格，用 1 表示障碍物。蛇需要移动到迷宫的右下角（`(n-1, n-2)` 和 `(n-1, n-1)`）。
>
> 每次移动，蛇可以这样走：
>
> - 如果没有障碍，则向右移动一个单元格。并仍然保持身体的水平／竖直状态。
> - 如果没有障碍，则向下移动一个单元格。并仍然保持身体的水平／竖直状态。
> - 如果它处于水平状态并且其下面的两个单元都是空的，就顺时针旋转 90 度。蛇从（`(r, c)`、`(r, c+1)`）移动到 （`(r, c)`、`(r+1, c)`）。
>     <img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/09/28/image-2.png" alt="img" style="zoom:33%;" />
> - 如果它处于竖直状态并且其右面的两个单元都是空的，就逆时针旋转 90 度。蛇从（`(r, c)`、`(r+1, c)`）移动到（`(r, c)`、`(r, c+1)`）。
>     <img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/09/28/image-1.png" alt="img" style="zoom: 33%;" />
>
> 返回蛇抵达目的地所需的最少移动次数。
>
> 如果无法到达目的地，请返回 `-1`。
>
>  
>
> **示例 1：**
>
> **<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/09/28/image.png" alt="img" style="zoom:33%;" />**
>
> ```
> 输入：grid = [[0,0,0,0,0,1],
>                [1,1,0,0,1,0],
>                [0,0,0,0,1,1],
>                [0,0,1,0,1,0],
>                [0,1,1,0,0,0],
>                [0,1,1,0,0,0]]
> 输出：11
> 解释：
> 一种可能的解决方案是 [右, 右, 顺时针旋转, 右, 下, 下, 下, 下, 逆时针旋转, 右, 下]。
> ```
>
> **示例 2：**
>
> ```
> 输入：grid = [[0,0,1,1,1,1],
>                [0,0,0,0,1,1],
>                [1,1,0,0,0,1],
>                [1,1,1,0,0,1],
>                [1,1,1,0,0,1],
>                [1,1,1,0,0,0]]
> 输出：9
> ```
>
>  
>
> **提示：**
>
> - `2 <= n <= 100`
> - `0 <= grid[i][j] <= 1`
> - 蛇保证从空单元格开始出发。

```python
class Solution:
    def minimumMoves(self, grid: List[List[int]]) -> int:
        '''
        动态规划：
        dp[i][j][s]: 尾移动到位置(i,j)时，所用的最少移动次数，s为蛇的运动方式，1为水平，0为竖直
        之所以用尾巴的位置，因为这可以使得在旋转的时候坐标不变
        转移方程：
        if grid[i][j] == 1:
            dp[i][j][0] = -1
            dp[i][j][1] = -1
        else:
            dp[i][j][0] = min([
                dp[i][j-1][0], # case1
                dp[i][j][1] # case4
            ]) + 1
            dp[i][j][1] = min([
                dp[i-1][j][1], # case2
                dp[i][j][0], # case3
            ]) + 1
        注意，需要根据障碍物和尾巴位置确定转移方式
        '''
        
        n = len(grid)
        dp = [[[1e5, 1e5] for _ in range(n)] for _ in range(n)]
        dp[0][0][0] = 0
        shuipin, chuizhi = True, True
        for i in range(n):
            for j in range(n):
                # 判断到达(i, j)位置和上一位置之间的关系，只要满足身体所在位置是空的即可
                if j + 1 < n and grid[i][j] == 0 and grid[i][j+1] == 0: shuipin = True
                else: shuipin = False
                if i + 1 < n and grid[i][j] == 0 and grid[i + 1][j] == 0: chuizhi = True
                else: chuizhi = False

                if i - 1 >= 0 and chuizhi:
                    dp[i][j][1] = min([dp[i][j][1], dp[i-1][j][1] + 1]) # case 2, 向下移动一个单元格
                if i - 1 >= 0 and shuipin:
                    dp[i][j][0] = min([dp[i][j][0], dp[i-1][j][0] + 1])
                if j - 1 >= 0 and shuipin:
                    dp[i][j][0] = min([dp[i][j][0], dp[i][j-1][0] + 1]) # case 1，向右移动一个单元格
                if j - 1 >= 0 and chuizhi:
                    dp[i][j][1] = min([dp[i][j][1], dp[i][j-1][1] + 1])

                if chuizhi and shuipin and grid[i+1][j+1] == 0:
                    dp[i][j][1] = min([dp[i][j][1], dp[i][j][0] + 1]) # case 4
                    dp[i][j][0] = min([dp[i][j][0], dp[i][j][1] + 1]) # case 3
        if dp[n-1][n-2][0] == 1e5:
            return -1
        else:
            return dp[n-1][n-2][0]
```

### 1223. 掷骰子模拟 <a name="roll">📌</a>

> 有一个骰子模拟器会每次投掷的时候生成一个 1 到 6 的随机数。
>
> 不过我们在使用它时有个约束，就是使得投掷骰子时，**连续** 掷出数字 `i` 的次数不能超过 `rollMax[i]`（`i` 从 1 开始编号）。
>
> 现在，给你一个整数数组 `rollMax` 和一个整数 `n`，请你来计算掷 `n` 次骰子可得到的不同点数序列的数量。
>
> 假如两个序列中至少存在一个元素不同，就认为这两个序列是不同的。由于答案可能很大，所以请返回 **模 `10^9 + 7`** 之后的结果。
>
> **示例 1：**
>
> ```
> 输入：n = 2, rollMax = [1,1,2,2,2,3]
> 输出：34
> 解释：我们掷 2 次骰子，如果没有约束的话，共有 6 * 6 = 36 种可能的组合。但是根据 rollMax 数组，数字 1 和 2 最多连续出现一次，所以不会出现序列 (1,1) 和 (2,2)。因此，最终答案是 36-2 = 34。
> ```
>
> **示例 2：**
>
> ```
> 输入：n = 2, rollMax = [1,1,1,1,1,1]
> 输出：30
> ```
>
> **示例 3：**
>
> ```
> 输入：n = 3, rollMax = [1,1,1,2,2,3]
> 输出：181
> ```
>
>  
>
> **提示：**
>
> - `1 <= n <= 5000`
> - `rollMax.length == 6`

```python
class Solution:
    def dieSimulator(self, n: int, rollMax: List[int]) -> int:
        '''
        动态规划：
        dp[i][j][k]: 第i次掷骰子，掷出j，并且j已经被连续掷出k次的组合数, i=0-5,j=0-5
        状态转移方程：
        - 第0次掷骰子，dp[0][j][1] = 1
        - 第i次掷骰子，掷出p，第i-1次是p'
            1. 如果p == p' and k + 1 <= rollMax[p]，dp[i][p][k+1] += dp[i-1][p'][k] (每次掷一次骰子，能组合出的数字长度就多一个，但组合数是依赖于其他等长的组合，以及短一位的组合的。因此这里的运算符是 += )
            2. 如果p != p', dp[i][p][1] += dp[i-1][p'][k]
        '''

        dp = []
        for i in range(n):
            dp.append([])
            for j in range(6):
                dp[i].append([])
                for k in range(16):
                    dp[i][j].append(0) # init = 0
        for j in range(6):
            dp[0][j][1] = 1
        #
        for i in range(1, n):
            for j in range(6):
                for k in range(1, rollMax[j]+1):
                    # 分别列举每次掷骰子的结果，这也是状态之一
                    for p in range(6):
                        if p != j:
                            dp[i][p][1] += dp[i-1][j][k]
                        else:
                            if k + 1 <= rollMax[p]:
                                dp[i][p][k+1] += dp[i-1][j][k]
        res = 0
        for j in range(6):
            for k in range(rollMax[j]+1):
                res += dp[n-1][j][k]
        return res % (10**9 + 7)
```

### 1140. 石子游戏 <a name="stoneGameII">📌</a>

> 爱丽丝和鲍勃继续他们的石子游戏。许多堆石子 **排成一行**，每堆都有正整数颗石子 `piles[i]`。游戏以谁手中的石子最多来决出胜负。
>
> 爱丽丝和鲍勃轮流进行，爱丽丝先开始。最初，`M = 1`。
>
> 在每个玩家的回合中，该玩家可以拿走剩下的 **前** `X` 堆的所有石子，其中 `1 <= X <= 2M`。然后，令 `M = max(M, X)`。
>
> 游戏一直持续到所有石子都被拿走。
>
> 假设爱丽丝和鲍勃都发挥出最佳水平，返回爱丽丝可以得到的最大数量的石头。
>
> **示例 1：**
>
> ```
> 输入：piles = [2,7,9,4,4]
> 输出：10
> 解释：如果一开始Alice取了一堆，Bob取了两堆，然后Alice再取两堆。爱丽丝可以得到2 + 4 + 4 = 10堆。如果Alice一开始拿走了两堆，那么Bob可以拿走剩下的三堆。在这种情况下，Alice得到2 + 7 = 9堆。返回10，因为它更大。
> ```
>
> **示例 2:**
>
> ```
> 输入：piles = [1,2,3,4,5,100]
> 输出：104
> ```
>
> **提示：**
>
> - `1 <= piles.length <= 100`
> - `1 <= piles[i] <= 10^4`

```python
class Solution:
    def stoneGameII(self, piles: List[int]) -> int:
        '''
        看起来像是动态规划
        - 首先一定要存储的是取到某一个位置时，已经得到的最大值或者后面能得到的最大值，
        - 但是光有位置是不够的，相同的位置有不同数量的堆可以取，所以我们还需存储当前的M值。

        由于本题中的状态是从后往前递推的，
            如：假如最后只剩一堆，一定能算出来最佳方案，但是剩很多堆时不好算（依赖后面的状态）。所以我们选择从后往前递推。

        dp[i][j]表示剩余[i : len - 1]堆时，M = j的情况下，先取的人能获得的最多石子
        - i + 2M >= len, dp[i][M] = sum[i : len - 1], 剩下的堆数能够直接全部取走，那么最优的情况就是剩下的石子总和
        - i + 2M < len, dp[i][M] = max(dp[i][M], sum[i : len - 1] - dp[i + x][max(M, x)]), 
            - 其中 1 <= x <= 2M，剩下的堆数不能全部取走，那么最优情况就是让下一个人取的更少。
            - 对于我所有的x取值，下一个人从x开始取起，M变为max(M, x)，所以下一个人能取dp[i + x][max(M, x)]，
            我最多能取sum[i : len - 1] - dp[i + x][max(M, x)]。
        - result = dp[0][1]
        '''
        N = len(piles)
        M = 2*N # M的取值范围：M = max(M, X), X <= 2M, M 最大为2N
        dp = [[0] * M for _ in range(N)]
        for i in range(N-1, -1, -1):
            for j in range(1, M): 
                if i + 2*j >= N:
                    dp[i][j] = sum(piles[i:])
                else:
                    for x in range(1, 2*j+1):
                        dp[i][j] = max([dp[i][j], sum(piles[i:]) - dp[i+x][max([j, x])]])
        return dp[0][1]
```



### 1144. 递减元素使数组呈锯齿状

> 给你一个整数数组 `nums`，每次 **操作** 会从中选择一个元素并 **将该元素的值减少 1**。
>
> 如果符合下列情况之一，则数组 `A` 就是 **锯齿数组**：
>
> - 每个偶数索引对应的元素都大于相邻的元素，即 `A[0] > A[1] < A[2] > A[3] < A[4] > ...`
> - 或者，每个奇数索引对应的元素都大于相邻的元素，即 `A[0] < A[1] > A[2] < A[3] > A[4] < ...`
>
> 返回将数组 `nums` 转换为锯齿数组所需的最小操作次数。
>
> **示例 1：**
>
> ```
> 输入：nums = [1,2,3]
> 输出：2
> 解释：我们可以把 2 递减到 0，或把 3 递减到 1。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [9,6,1,6,2]
> 输出：4
> ```
>
> **提示：**
>
> - `1 <= nums.length <= 1000`
> - `1 <= nums[i] <= 1000`

```python
class Solution:
    def movesToMakeZigzag(self, nums: List[int]) -> int:
        '''
        明显是动态规划，总的最小操作次数最小即锯齿波形最平缓，即每次都操作最少的次数使得符合要求
        dp[i]: 遍历到i位的时候，要对i位操作多少次，使得满足条件
        遍历两次取最少好了
        - 第一种情况：
            if i % 2 == 1: # 操作奇数索引
                if nums[i-1] > nums[i] < nums[i+1] is False:
                    dp[i] = nums[i] - min([nums[i-1], nums[i+1]]) + 1
        - 第二种情况：
            if i % 2 == 0: # 操作偶数索引
                if nums[i-1] > nums[i] < nums[i+1] is False:
                    dp[i] = nums[i] - min([nums[i-1], nums[i+1]]) + 1

        '''
        if len(nums) == 1:
            return 0
        n = len(nums)
        dp = [0] * n
        cases = [1, 0]
        res = math.inf

        for case in cases:
            for i in range(n):
                if i % 2 == case: # 操作奇数 / 偶数索引
                    if i-1 >= 0 and i+1 < n and (nums[i-1] > nums[i] < nums[i+1]) is False:
                        dp[i] = nums[i] - min([nums[i-1], nums[i+1]]) + 1
                    elif i-1 < 0 and (nums[i] < nums[i+1]) is False:
                        dp[i] = nums[i] - nums[i+1] + 1
                    elif i+1 >= n and (nums[i-1] > nums[i]) is False:
                        dp[i] = nums[i] - nums[i-1] + 1
            res = min([res, sum(dp)])
            dp = [0] * n

        return res
```

### 百度校招-2022.9.13-01反转

> **题目解析以及代码**
>
> 关注公众号:塔子哥学算法，搜索“P1024”即可得到对应题解
>
> **题目大意**
>
> 给一个01串，每次可以选择两个连续的下标，并对该下标的元素取反（0变1，1变0），问是否可以将串转换为只包含0或只包含1
>
> **输入描述**
>
> 输入一个只包含字符0或者1的字符串s（1<=字符串长度<=1000000)
>
> **输出描述**
>
> 如果满足题意输出"yes",否则输出"no"(不含引号)
>
> **样例**
>
> **输入1**
>
> ```none
> 1010010
> ```
>
> **输出1**
>
> ```none
> yes
> ```
>
> **输入2**
>
> ```none
> 10100100
> ```
>
> **输出2**
>
> ```none
> no
> ```

```python
def myfunc(input_str):
    '''
    分类讨论反转为0或1的情况
    贪心：分别在每一位上评估是否要为0或者1，如果最后能全部转为0或者1，则可以满足要求
    '''
    def check(input_str, flag):
        for i in range(len(input_str)-1):
            if input_str[i] == flag:
                continue
            else:
                # 连续改两位
                if input_str[i] == '0': input_str[i] = '1'
                elif input_str[i] == '1': input_str[i] = '0'

                if input_str[i+1] == '0': input_str[i+1] = '1'
                elif input_str[i+1] == '1': input_str[i+1] = '0'
        # 上述代码没有评价最后一个字符是否满足要求
        if input_str[-1] == flag:
            return True
        else:
            return False
    if check(input_str, '0') or check(input_str, '1'):
        return 'yes'
    else: 
        return 'no'

input_str = input()
input_str = list(input_str)
res = myfunc(input_str)
print(res)
```

