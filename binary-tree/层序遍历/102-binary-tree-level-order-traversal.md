# 102. Binary Tree Level Order Traversal (二叉树的层序遍历)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, return the level order traversal of its nodes' values (i.e., from left to right, level by level).

## Constraints

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

---

## 1. 题目代码 (Python)

使用 `collections.deque` 实现广度优先搜索 (BFS)，通过每层开始前的队列长度 `cur_lvl_sz` 来划定层级边界。

```python
from collections import deque

class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        # 边界情况：空树
        if root is None:
            return []
        
        res = []
        q = deque([root])
        
        while q:
            level = []
            # 锁定当前层的节点数量，确保只处理当前层的元素
            cur_lvl_sz = len(q)
            
            for i in range(cur_lvl_sz):
                cur = q.popleft()
                level.append(cur.val)
                
                # 判断子节点是否存在并加入队列
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
            
            # 将处理完的当前层结果存入最终列表
            res.append(level)
            
        return res
```

---

## 2. 复杂度分析

**时间复杂度:** $O(n)$

原因：二叉树中的每个节点都会被访问且仅访问一次（入队一次，出队一次）。队列操作的时间复杂度为 $O(1)$，总耗时与节点数 $n$ 成线性关系。

**空间复杂度:** $O(n)$

原因：主要内存消耗在于队列 `q`。在最坏情况下（例如满二叉树），队列中最多会同时存在最后一层的所有节点，约为 $n/2$ 个，因此空间复杂度为 $O(n)$。

---

## 3. 语法提示

在处理 Python 的二叉树题目时，关于判断节点是否为空，以下两种写法在逻辑效果上是完全一样的：

- `if cur.left:` （推荐，Pythonic 写法）
- `if cur.left is not None:` （更严谨的显式检查）

**注意：** 不要写成 `if not cur.left:`，因为那是当节点不存在时才会执行。
