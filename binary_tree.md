---
title: Binary Tree 
date: 2022-08-29 21:00:00
updated: 2022-12-07 17:00:00
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

1. **从中序和后序遍历序列构造二叉树**：

    > 思路：构造树的前提是识别根节点
    >
    > 对于任意一颗树而言，前序遍历的形式总是
    > 	[ 根节点, [左子树的前序遍历结果], [右子树的前序遍历结果] ]
    > 而中序遍历的形式总是
    > 	[ [左子树的中序遍历结果], 根节点, [右子树的中序遍历结果] ]
    >
    > 只要我们在中序遍历中定位到根节点，那么我们就可以分别知道左子树和右子树中的节点数目。
    > 由于同一颗子树的前序遍历和中序遍历的长度显然是相同的，因此我们就可以对应到前序遍历的结果中，
    > 对上述形式中的所有左右括号进行定位。

1. **不要返回任何值，在原树上返回**：不要试图新建任何节点，而是在已有节点上**操纵指针**，参考[这个](#flatten)

1. [合并二叉树](#combine_two_tree)：最好再温习一下

1. [二叉树的直径](#r_of_tree)：直径长度可以被拆分为三个部分：一个根结点、左子树、右子树。直径路径（所包含的节点数目）即为上述两个子树的深度之和 + 1。路径长度是节点数目 - 1。

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

11. [105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/?favorite=2cktkvj)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        '''
        思路：构造树的前提是识别根节点
        
        对于任意一颗树而言，前序遍历的形式总是
        [ 根节点, [左子树的前序遍历结果], [右子树的前序遍历结果] ]
        而中序遍历的形式总是
        [ [左子树的中序遍历结果], 根节点, [右子树的中序遍历结果] ]
        
        只要我们在中序遍历中定位到根节点，那么我们就可以分别知道左子树和右子树中的节点数目。
        由于同一颗子树的前序遍历和中序遍历的长度显然是相同的，因此我们就可以对应到前序遍历的结果中，
        对上述形式中的所有左右括号进行定位。
        '''
        def find_sub(seq, subseq):
            if len(subseq)>0:
                # 返回subseq元素在seq中的排列顺序
                seq_d = {}
                for idx, i in enumerate(seq):
                    seq_d[i] = idx # key: num, value: idx
                re_idx = [seq_d.get(i) for i in subseq]
                re_idx.sort() # 按在seq中的顺序排序
                return [seq[i] for i in re_idx]
            else:
                return []
        def func(pre_list, in_list):
            # 给一个前、中序遍历，返回一个由遍历结果组成的树
            if len(pre_list) == 0:
                return None
            elif len(pre_list) == 1:
                return TreeNode(val=pre_list[0])
            elif len(pre_list) == 2:
                if in_list.index(pre_list[0]) == 0:
                    return TreeNode(val=pre_list[0], right=TreeNode(val=pre_list[1]))
                else:
                    return TreeNode(val=pre_list[0], left=TreeNode(val=pre_list[1]))
            else:
                h = TreeNode(val=pre_list[0])
                root_idx = in_list.index(h.val)
                left_list_in = in_list[0:root_idx]
                right_list_in = in_list[root_idx+1:]
                # 在pre[1:]里查找左右子树的遍历
                left_list_pre = find_sub(pre_list, left_list_in)
                right_list_pre = find_sub(pre_list, right_list_in)
                # 递归调用
                if len(left_list_pre) > 0:
                    l = func(left_list_pre, left_list_in)
                    h.left = l
                if len(right_list_pre) > 0:
                    r = func(right_list_pre, right_list_in)
                    h.right = r
                return h
        return func(preorder, inorder)

```

12. [114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/description/?favorite=2cktkvj) <a name="flatten">📌</a>

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def flatten(self, root: Optional[TreeNode]) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        '''
        思路: 先遍历一边获得顺序，然后修改树
        '''
        if root is not None:
            route = [] # 按照前序存储所有的节点对象
            def func(t):
                if t is not None:
                    route.append(t)
                if t and t.left:
                    func(t.left)
                if t and t.right: 
                    func(t.right)
            func(root)
            for i in range(1, len(route)):
                prev, curr = route[i-1], route[i]
                prev.left = None
                prev.right = curr
```

13. [226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/description/?favorite=2cktkvj)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        '''
        思路：
        从根节点开始，如果存在左右子树，交换之
        '''
        def func(root):
            if root is not None:
                # exchange
                t = root.left
                root.left = root.right
                root.right = t
                if root.left is not None:
                    func(root.left)
                if root.right is not None:
                    func(root.right)
            return root
        return func(root)
```

14. [617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/description/?favorite=2cktkvj) <a name="combine_two_tree">📌</a>

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        # 返回root1好了
        def merge(node1, node2):
            if node1 is not None and node2 is not None:
                node1.val += node2.val
                if node1.left is not None or node2.left is not None:
                    if node1.left is None:
                        node1.left = TreeNode()
                    merge(node1.left, node2.left)
                if node1.right is not None or node2.right is not None:
                    if node1.right is None:
                        node1.right = TreeNode()
                    merge(node1.right, node2.right)
            elif node1 is not None and node2 is None:
                # 边界条件1: 两个节点之一是最后一个节点，那就不用看后面的字节点了
                return None
            elif node1 is None and node2 is not None:
                # 边界条件1
                return TreeNode(node2.val)
            else: 
                # 边界条件2: 俩节点都是边界了
                return None
        res = merge(root1, root2)
        if root1 is not None:
            return root1
        else:
            return res
```

15. [543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/description/?favorite=2cktkvj) <a name="r_of_tree">📌</a>

> 给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。
>
> **示例 :**
> 给定二叉树
>
> ```
>           1
>          / \
>         2   3
>        / \     
>       4   5    
> ```
>
> 返回 **3**, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        '''
        思路：注意，这里的直径长度可以不经过原点
        直径长度可以被拆分为三个部分：一个根结点、左子树、右子树。
        直径路径（所包含的节点数目）即为上述两个子树的深度之和 + 1
        ⭐️ 用递归做深度遍历来解决
        '''
        self.mmax = -1
        def depth(node):
            # 返回一个根结点出发的最大深度（节点个数）
            if node is not None:
                if node.left is not None:
                    left = depth(node.left)
                else: left = 0
                if node.right is not None:
                    right = depth(node.right)
                else: right = 0
                self.mmax = max(self.mmax, left + right + 1) # 路径中的节点数目等于 L + R + 1
                return max(left, right) + 1
            else:
                return 0
        depth(root)     
        return self.mmax - 1
```

