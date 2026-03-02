# 894. 所有可能的真二叉树 (All Possible Full Binary Trees)

**Difficulty:** Medium

## Problem

Given an integer `n`, return a list of all possible full binary trees with `n` nodes. Each node of each tree in the answer must have `Node.val == 0`.

A full binary tree is a binary tree where each node has exactly 0 or 2 children.

## Constraints

- `1 ≤ n ≤ 20`

---

## 破题核心：分治与递归

**思考起点：** 一棵真二叉树的左右子树，必然也都是真二叉树。

**结论：** 这意味着我们可以把构建 $n$ 个节点的大树，拆解为构建更小节点数的左子树和右子树的问题。递归是这道题的灵魂。

---

## 寻找规律与边界（Base Case & 剪枝）

在动手写递归之前，必须先明确什么情况是合法的，什么情况该停下。

**规律发现（剪枝）：** 真二叉树的节点数 $n$ 必须是奇数。如果传入的是偶数，绝对无法构成，直接返回空列表 `[]`。

**递归终点（Base Case）：** 当节点数缩小到最基本的情况，即 $n = 1$ 时，形态是唯一确定的，直接返回包含一个根节点的列表：`[TreeNode(0)]`。

---

## 核心拆分逻辑（高光推导）

当 $n > 1$（且为奇数）时，我们需要把 $n$ 个节点分"家"：

1. 拿出一个节点作为当前的 root，剩下 $n-1$ 个节点。
2. 将这 $n-1$ 个节点分配给左子树（设为 $i$ 个）和右子树（设为 $j$ 个）。

**关键推论：** 因为左右子树本身也必须是真二叉树，所以 $i$ 和 $j$ 都必须是奇数。

**代码落地：** 利用步长为 2 的 for 循环，完美枚举所有合法的左子树节点数：

```python
for i in range(1, n-1, 2):
    j = n - i - 1
    leftSubTrees = self.build(i)
    rightSubTrees = self.build(j)
```

---

## 拼装组合（最后一块拼图）

通过上面的步骤，我们拿到了两堆积木：一堆合法的左子树 `leftSubTrees`，一堆合法的右子树 `rightSubTrees`。

**如何把它们和当前的 root 组装起来？** 使用嵌套循环 (Nested For Loop) 来穷举所有的配对组合（笛卡尔乘积）：

- 外层循环遍历每一个左子树
- 内层循环遍历每一个右子树
- 在最内层，创建一个新的 `root = TreeNode(0)`
- 将选出的左右子树分别挂在 `root.left` 和 `root.right` 上
- 将组装好的 root 收集到当前层的结果列表 `res` 中

---

## 进阶性能优化：记忆化搜索 (Memoization)

在递归拆分的过程中（比如求 $n=7$ 时，会多次求 $n=3$ 的情况），会产生大量的重复计算。

**解决方案：** 引入一个"记事本"字典 `self.memo`。

**执行流程：**

1. 进入递归第一步：先查字典 `if n in self.memo:`，如果算过，直接返回结果，拦截重复计算。
2. 算出当前 $n$ 的结果 `res` 后，在返回前先记录一笔：`self.memo[n] = res`。

**巧妙的写法：** 可以直接将 Base Case 初始化在字典中 `self.memo = {1: [TreeNode(0)]}`，这不仅省去了判断代码，还能在底层实现叶子节点内存复用，进一步提升性能。

---

## 架构探讨：需要单独的 build 函数吗？

**方案 A（直接递归）：** 直接让主函数 `allPossibleFBT(self, n)` 自己调用自己。配合全局的 `self.memo`，代码最简短直接。

**方案 B（辅助函数）：** 在主函数内嵌套定义一个 `def build(k):`。好处是可以让 memo 成为每次测试用例独立的局部变量，防止不同测试用例之间的数据污染（虽然本题节点值固定，污染无妨，但在其他图论题中是好习惯）。

---

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def __init__(self):
        self.memo = {}

    def allPossibleFBT(self, n: int) -> List[Optional[TreeNode]]:
        if n in self.memo:
            return self.memo[n]
        if n % 2 == 0:
            return []
        if n == 1:
            self.memo[1] = [TreeNode(0)]
            return self.memo[1]
        res = []
        for i in range(1, n - 1, 2):
            j = n - 1 - i
            leftsubtree = self.allPossibleFBT(i)
            rightsubtree = self.allPossibleFBT(j)
            for l in leftsubtree:
                for r in rightsubtree:
                    root = TreeNode(0)
                    root.left = l
                    root.right = r
                    res.append(root)
        self.memo[n] = res
        return self.memo[n]
```
