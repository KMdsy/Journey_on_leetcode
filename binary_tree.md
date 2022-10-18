---
title: Binary Tree 
date: 2022-08-29 21:00:00
updated: 2022-09-15 23:00:00
tag:
- leetcode
- binary tree
---

本章主要总结关于二叉树的相关题目框架，以及基础知识的补全

## 基础知识

- 前序/中序/后序遍历：前/中/后表示每次遍历的时候访问**根节点**的位置，分别表示：1）在访问根节点后访问二叉，2）先访问左叉，再访问根，最后访问右叉，3）先访问左右两个叉，最后访问根节点。
- ❗️关于用**回溯**解决二叉树的重点：判断出符合题解的停止回溯的条件❗️。
- 二叉搜索树：二叉搜索树是一个有序树，特征如下：
    - 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值；
    - 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值；
    - 它的左、右子树也分别为二叉搜索树




## 解题框架

```java
// 二叉树
void traverse(TreeNode root) {
  // 前序遍历
  traverse(root.left)
  // 中序遍历
  traverse(root.right)
  // 后序遍历
}

// N叉树
class TreeNode {
  int val;
  TreeNode[] children;
}
void traverse(TreeNode root) {
  for (TreeNode child : root.children)
  traverse(child)
}

// 层序遍历模版
void bfs(TreeNode root) {
    Queue<TreeNode> queue = new ArrayDeque<>(); // 维护一个列表，存储某一层的所有非空节点
    queue.add(root);
    while (!queue.isEmpty()) { // 循环每一层中的节点值
        TreeNode node = queue.poll(); // Java 的 pop 写作 poll(), 每处理好一个值，列表中pop掉
        if (node.left != null) {
            queue.add(node.left);
        }
        if (node.right != null) {
            queue.add(node.right);
        }
    }
}
```



## 题目思路记忆要点

1. **二叉树的最大深度**：写一个回溯，查询所在树的最大深度。
    - 回溯的临界条件就是查到树的最大深度，标志是：到达了空节点，返回0。
    - 除此之外的逻辑为：最大深度为 `1+max(depth(left), depth(right))`
1. **二叉树的最短路径**：最短路径是从根节点到叶子节点的最大深度，写一个回溯，查询所在树的最小深度。
    - 回溯的临界条件是查询到树的叶子节点，标志是：某个节点没有任何子节点，因此返回1（代表当前节点）。
    - 除此之外的逻辑是：当树只有一个子节点的时候，最短深度为`1 + depth(left/right)`；当树有两个子节点的时候，最短深度为`1 + min(depth(left), depth(right))`;树没有节点的时候，最短深度为1。
    - 注意整个树都为空的临界情况。

## 题目

1.  [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        out = []
        def solution(root, out_list):
            if root and root.left:
                solution(root.left, out_list)
            if root:
                out_list.append(root.val)
            if root and root.right:
                solution(root.right, out_list)
            return out_list
        return solution(root, out)
```

2.  [101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        out_left = []
        out_right = []
        # 一个前序遍历
        def iter_font(root, out):
            if root:
                out.append(root.val)
                if root.left: iter_font(root.left, out)
                else: out.append(-1000)
                if root.right: iter_font(root.right, out)
                else: out.append(-1000)
            else: out.append(-1000)
            return out
        # 为了轴对称，左右倒置
        def iter_behind(root, out):
            if root:
                out.append(root.val)
                if root.right: iter_behind(root.right, out)
                else: out.append(-1000)
                if root.left: iter_behind(root.left, out)
                else: out.append(-1000)
            else: out.append(-1000)
            return out

        if root.left is None and root.right is None:
            return True
        else:
            iter_font(root.left, out_left)
            iter_behind(root.right, out_right)
        if len(out_left) == len(out_right):
            for i in range(len(out_left)):
                if out_left[i] != out_right[i]:
                    return False
            return True
        else:
            return False
```

3.  [104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # 给定树的最大深度
        def iter(root):
            if root is None:
                return 0
            else:
                left = iter(root.left)
                right = iter(root.right)
                # 本次的树的最大深度，等于max(左边深度 / 右边深度) + 1, 1是当前节点的深度
                return 1 + max([left, right])
        return iter(root)
```

4.  [111. 二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        def iter(root):
            if root is not None and root.right is None and root.left is None:
                # 遍历到叶子节点，终止遍历
                return 1
            else:
                # 其他情况下，树的最小深度是 min(左边的最小深度 / 右边的最小深度) + 1
                if root.right: right = iter(root.right)
                else: right = -1
                if root.left: left = iter(root.left)
                else: left = -1

                if right > 0 and left > 0:
                    # 树的两侧都有节点
                    return 1 + min([left, right]) # 1 是本身的深度
                elif right < 0 and left < 0:
                    # 树的两侧都没有节点
                    return 1
                else:
                    # 树的一侧没有节点
                    return 1 + max([left, right]) # 1 + 有节点的那一侧
        return iter(root)
```

5. [572. 另一棵树的子树](https://leetcode.cn/problems/subtree-of-another-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSubtree(self, root: TreeNode, subRoot: TreeNode) -> bool:
        def compare(t1, t2):
            # 前序遍历比较两个树，如果不一样则返回False，一样则返回True
            if t1 is None and t2 is None: 
                # 条件1: 两个树都为空
                return True
            elif (t1 is None or t2 is None) or t1.val != t2.val:
                return False
            else:
                # 条件2: 如果根节点的结构相同（都非空）且值相同，则开始比较子节点，左右子树都相同
                res_left = compare(t1.left, t2.left)
                res_right = compare(t1.right, t2.right)
                if res_left and res_right:
                    return True
        # 从root的每一个节点出发，比较子树
        def iter(t):
            # 遍历root树的每个节点
            if compare(t, subRoot):
                return True
            elif t.left and iter(t.left):
                return True
            elif t.right and iter(t.right):
                return True
            else:
                return False
        return iter(root)
```

6.  [96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

一个在二叉树上的动态规划题目

```python
class Solution:
    def numTrees(self, n: int) -> int:
        # 动态规划的要点就是：确定递归
        # 首先以要解决的问题为最终的函数，分解问题，找到最终问题与子问题的递归表达式
        # 最后自下而上（动态规划的特性要求记录子问题的解）计算最终解，注意边界。

        # DP问题：给定n个节点，可以构造出多少二叉搜索树
        dp = {} # key: n, value: solution
        dp[0] = 1 # 注意这里的边界条件，也是1
        dp[1] = 1
        # 问题分解
        # G(n) = f(1) + ... + f(n), f(j)为j当根节点时的二叉搜索树数目
        # f(j) = G(j-1) * G(n-j) # 比i小的节点有i-1个, 比i大的节点有n-1个
        # j为根节点的树的数目是两侧子树数目相乘——排列组合
        for i in range(2, n+1):
            dpj = 0
            for j in range(1, i + 1):
                fj = dp[j-1] * dp[i-j]
                dpj += fj
            dp[i] = dpj
        return dp[n]
        # i = 2 的时候，求解dp[2]
        # dp[2] = f(1) + f(2)
        # j = 1, f(1) = dp[0] * dp[1] = 0
```

7.  [95. 不同的二叉搜索树 II](https://leetcode.cn/problems/unique-binary-search-trees-ii/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def generateTrees(self, n: int) -> List[Optional[TreeNode]]:
        def func(node_list):
            node_list.sort()
            # 返回由node_list组成的二叉搜索树
            if len(node_list) == 0:
                return []
            elif len(node_list) == 1:
                return [TreeNode(node_list[0])]
            else:
                # 由两个及以上的节点组成二叉搜索树的时候
                res = []
                for idx, r in enumerate(node_list):
                    # 确定根节点 r
                    left_tree = func(node_list[:idx]) # list of tree
                    right_tree = func(node_list[idx+1:]) # list of tree
                    if len(left_tree) != 0 and len(right_tree) != 0:
                        for ltree in left_tree:
                            for rtree in right_tree:
                                res.append(TreeNode(r, left=ltree, right=rtree))
                    elif len(left_tree) == 0 and len(right_tree) == 0:
                        res.append(TreeNode(r))
                    else:
                        if len(left_tree) == 0:
                            res += [TreeNode(r, right=rtree) for rtree in right_tree]
                        else:
                            res += [TreeNode(r, left=ltree) for ltree in left_tree]
                return res
        return func(list(range(1, n+1)))
```

8.  [98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def listcmp(l, v, mode):
            res = []
            if mode == 'small':
                for i in l:
                    if i < v: res.append(True)
                    else: res.append(False)
            elif mode == 'large':
                for i in l:
                    if i > v: res.append(True)
                    else: res.append(False)
            return res
        def isvalid(tree: Optional[TreeNode]):
            if tree is None:
                return True, []
            elif tree.left is None and tree.right is None:
                return True, [tree.val]
            else:
                v = tree.val
                is_left_valid, left_numbers = isvalid(tree.left)
                is_right_valid, right_numbers = isvalid(tree.right)
                # 有两种可能，一边是none，或者两边都不是none
                if (len(left_numbers) == 0 or all(listcmp(left_numbers, v, 'small'))) and \
                (len(right_numbers) == 0 or all(listcmp(right_numbers, v, 'large'))):
                    if is_left_valid and is_right_valid:
                        return True, left_numbers + right_numbers + [v]
                    else: return False, left_numbers + right_numbers + [v]
                else: return False, left_numbers + right_numbers + [v]
        return isvalid(root)[0]
```

9.  [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        def myfunc(res, non_empty):
            # 返回一个层序访问结果，以及第二层中非空节点的列表
            new_non_empty = []
            this_res = []
            for node in non_empty:
                if node.left is not None:
                    this_res.append(node.left.val)
                    new_non_empty.append(node.left)
                if node.right is not None:
                    this_res.append(node.right.val)
                    new_non_empty.append(node.right)
            if len(this_res) > 0:
                res.append(this_res)
            return res, new_non_empty
        if root is None:
            return []
        else:
            res = [[root.val]]
            non_empty = [root]
            while len(non_empty) > 0:
                res, non_empty = myfunc(res, non_empty)
        return res
```

10. [103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        queue = [] # 全部正序存储
        res = []
        reverse_flag = False

        if root is None:
            return []

        queue.append(root)
        while len(queue) > 0:
            order = list(range(len(queue)))
            if reverse_flag: order = order[::-1]
            tmp_queue = []
            tmp_res = []
            for idx in order:
                node = queue[idx] # 弹出正在处理的
                tmp_res.append(node.val)
                if reverse_flag:
                    if node.right is not None:
                        tmp_queue.append(node.right)
                    if node.left is not None:
                        tmp_queue.append(node.left)
                else:
                    if node.left is not None:
                        tmp_queue.append(node.left)                   
                    if node.right is not None:
                        tmp_queue.append(node.right)
            if reverse_flag:
                tmp_queue = tmp_queue[::-1]
            queue = tmp_queue
            res.append(tmp_res)
            reverse_flag = False if reverse_flag else True 
        return res
```

