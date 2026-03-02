# 107. Binary Tree Level Order Traversal II (二叉树的层序遍历 II)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, return the bottom-up level order traversal of its nodes' values (i.e., from left to right, level by level from leaf to root).

## Constraints

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

---

## 第一阶段：思维纠偏 —— 什么时候入队？什么时候记录值？

在写层序遍历时，最容易产生逻辑混淆的地方在于"当下"与"未来"的处理。请复习时问自己以下两个问题：

**问题 1：我该把什么东西加入 current_level 列表？**

逻辑直觉：既然我要记录这一层的值，那么当我从队列里**取出一个节点（popleft）**时，这个节点就是"当下"的成员。

结论：直接把 `cur.val` 加入 `current_level`。

**问题 2：我该在什么时候把节点加入队列 q？**

逻辑直觉：队列 `q` 代表的是"待处理的未来"。当我处理当前的 `cur` 时，它的左右孩子属于"下一层"。

结论：只要看到 `cur.left` 或 `cur.right` 存在，就立刻把它们送入队列。它们会乖乖排在当前层所有节点的后面，等待下一轮 while 循环处理。

---

## 第二阶段：性能博弈 —— 为什么要用 appendleft？

题目要求"自底向上"，即最后一层要排在结果列表的最前面。我们有三种选择：

**`res.insert(0, level)`：**

- 评价：差。Python 列表是连续存储的，在索引 0 插入会导致后面所有元素整体向后平移。
- 复杂度：每次插入 $O(k)$，整体复杂度会退化。

**`res.append(level)` + `res[::-1]`：**

- 评价：好。这是最稳妥的做法，先正常收集，最后一次性反转。
- 复杂度：最后反转耗时 $O(H)$，相对于 $O(N)$ 遍历来说非常小。

**`res = deque()` + `res.appendleft(level)`：**

- 评价：优。利用双端队列的链表特性，在头部插入是 $O(1)$。
- 结论：这能让你在遍历的过程中，就自然而然地形成了"倒序"排列，代码逻辑最丝滑。

---

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

from collections import deque
class Solution:
    def levelOrderBottom(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        res = deque()
        q = deque()
        q.append(root)
        while q:
            sz = len(q)
            level = []
            for i in range(sz):
                cur = q.popleft()
                if cur.left:
                    q.append(cur.left)            
                if cur.right:
                    q.append(cur.right)
                level.append(cur.val)
            res.appendleft(level)
        return list(res)
```
