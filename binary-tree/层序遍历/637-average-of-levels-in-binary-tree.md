# 637. Average of Levels in Binary Tree (二叉树的层平均值)

**Difficulty:** Easy

## Problem

Given the root of a binary tree, return the average value of the nodes on each level in the form of an array.

---

## Solution

Nothing special — exactly the same pattern as 515, just simple BFS.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
from collections import deque
class Solution:
    def averageOfLevels(self, root: Optional[TreeNode]) -> List[float]:
        res = []
        if root is None:
            return res
        q = deque()
        q.append(root)
        while q:
            level_sum = 0
            sz = len(q)
            for i in range(sz):
                cur = q.popleft()
                level_sum += cur.val
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
            res.append(level_sum/sz)
        return res
```
