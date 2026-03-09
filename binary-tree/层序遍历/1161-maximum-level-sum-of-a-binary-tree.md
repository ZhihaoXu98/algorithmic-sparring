# 1161. Maximum Level Sum of a Binary Tree (最大层内元素和)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, the level of its root is 1, the level of its children is 2, and so on. Return the smallest level x such that the sum of all the values of nodes at level x is maximal.

---

## Solution

Nothing to note — simple BFS.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
from collections import deque
class Solution:
    def maxLevelSum(self, root: Optional[TreeNode]) -> int:
        res = 0
        max_sum = float('-infinity')
        q = deque()
        depth = 1
        q.append(root)
        while q:
            sz = len(q)
            level_sum = 0
            for i in range(sz):
                cur = q.popleft()
                level_sum += cur.val
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
            if level_sum > max_sum:
                max_sum = level_sum
                res = depth
            depth += 1
        return res
```
