# LeetCode 662: 二叉树最大宽度 (Maximum Width of Binary Tree)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, return the maximum width of the given tree. The maximum width of a tree is the maximum width among all levels. The width of one level is defined as the length between the end-nodes (the leftmost and rightmost non-null nodes), where the null nodes between the end-nodes that would be present in a complete binary tree extending down to that level are also counted into the length calculation.

---

## 引导式解题笔记

这篇笔记记录了从零开始推导 LeetCode 662 题目的全过程。核心在于通过不断提问和思考，将直觉转化为严谨的算法。

---

## 1. 寻找规律：完全二叉树的编号 (Index)

题目中提到，两端点之间的 null 节点也计入宽度。这与**完全二叉树（Complete Binary Tree）**在数组中的存储结构高度吻合。

**规律：** 如果一个父节点的编号是 $i$：

- 它的左子节点编号必定是：$2 \times i$
- 它的右子节点编号必定是：$2 \times i + 1$

---

## 2. 算法选择：BFS (广度优先搜索)

由于是求"每一层的宽度"，自然想到使用 BFS 按层遍历。

**关键点：** 不仅要在队列 (Queue) 中存储节点 (Node) 本身，还需要把节点对应的编号 (Index) 绑定在一起存入队列。即队列中存储的数据结构为：`Tuple(node, index)`。

---

## 3. 核心逻辑：如何计算宽度？

在 BFS 遍历时，当准备处理某一层的所有节点（进入该层的 for 循环前），队列里刚好装着这一层的全部节点。

- **最左边节点：** 队列的队首元素 `q[0]`
- **最右边节点：** 队列的队尾元素 `q[-1]`
- **本层宽度公式：** 最右节点编号 - 最左节点编号 + 1

---

## 4. 面试核心考点 / 隐藏坑点：整数溢出 (Integer Overflow)

**风险：** 如果树非常深（例如 3000 层）且向一侧严重倾斜，最底层的节点编号会达到 $2^{3000}$。在 C++ 或 Java 中会直接导致整数溢出。虽然 Python 会自动处理超大整数，但计算大整数乘法和加法也会消耗大量时间和内存。

**破局思路（对齐基准线）：** 计算宽度只关心"相对距离"，不关心"绝对编号"。

**解决方案：** 每一层都重新"对齐"。提取这一层队首元素的编号作为 `start_id`。在计算当前层左右子节点的编号时，不再使用原有的绝对编号，而是用减去 `start_id` 后的相对编号。

---

## 5. 代码实现对比

这里展示两种写法。在 Python 环境下两者都能 Pass，但在真实的面试环境（考虑到其他语言的特性）中，进阶版本是拿满分的关键。

### 5.1 基础版本（直接使用绝对编号）

这是推导出的第一版逻辑。在 Python 中得益于大整数自动处理机制，可以直接运行通过，逻辑非常清晰。但存在大数字计算的性能隐患以及在其他语言中的溢出风险。

```python
from collections import deque
from typing import Optional

# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def widthOfBinaryTree(self, root: Optional['TreeNode']) -> int:
        max_width = 0
        if root is None:
            return 0
            
        q = deque()
        q.append((root, 0))
        
        while q:
            sz = len(q)
            # 计算当前层的宽度
            width = q[-1][1] - q[0][1] + 1
            max_width = max(max_width, width)
            
            # 遍历当前层的所有节点
            for i in range(sz):
                cur = q.popleft()
                cur_id = cur[1]
                
                # 直接使用父节点的绝对编号推导子节点
                if cur[0].left:
                    q.append((cur[0].left, 2 * cur_id))
                if cur[0].right:
                    q.append((cur[0].right, 2 * cur_id + 1))            
                    
        return max_width
```

### 5.2 进阶优化版本（引入基准对齐 - 面试满分写法）

这一版在入队前，通过减去当前层的起点 `start_id`，将极端的绝对编号转换为较小的相对编号，完美避开了可能存在的溢出问题。

```python
from collections import deque
from typing import Optional

class Solution:
    def widthOfBinaryTree(self, root: Optional['TreeNode']) -> int:
        if root is None:
            return 0
            
        max_width = 0
        q = deque()
        q.append((root, 0))  # 存入 (节点, 初始编号)
        
        while q:
            sz = len(q)
            # 获取本层基准对齐值（最左侧节点的编号）
            start_id = q[0][1] 
            
            # 计算当前层的宽度
            width = q[-1][1] - start_id + 1
            max_width = max(max_width, width)
            
            # 遍历当前层的所有节点
            for _ in range(sz):
                cur_node, cur_id = q.popleft()
                
                # 计算相对编号（防溢出核心逻辑）
                relative_id = cur_id - start_id 
                
                # 将左右子节点及它们的新编号推入队列
                if cur_node.left:
                    q.append((cur_node.left, 2 * relative_id))
                if cur_node.right:
                    q.append((cur_node.right, 2 * relative_id + 1))            
                    
        return max_width
```

---

## 6. 复杂度分析

**时间复杂度：** $O(N)$，其中 $N$ 是二叉树的节点数。每个节点都会被访问并进出队列一次。

**空间复杂度：** $O(N)$。BFS 队列中最多同时存放一整层的节点。对于完全二叉树的最底层，最多包含大约 $N/2$ 个节点，因此空间复杂度为 $O(N)$。
