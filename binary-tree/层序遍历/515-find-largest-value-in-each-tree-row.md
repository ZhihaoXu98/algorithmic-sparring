# 515. Find Largest Value in Each Tree Row (在每个树行中找最大值)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, return an array of the largest value in each row of the tree.

---

## Solution

Simple BFS — nothing special.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
from collections import deque
class Solution:
    def largestValues(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        if root is None:
            return res
        q = deque()
        q.append(root)
        while q:
            val = float("-infinity")
            sz = len(q)
            for i in range(sz):
                cur = q.popleft()
                val = max(val, cur.val)
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
            res.append(val)
        return res
```
