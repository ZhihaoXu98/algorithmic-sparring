# 1302. Deepest Leaves Sum (层数最深叶子节点的和)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, return the sum of values of its deepest leaves.

---

## Solution

Nothing to note — just simple BFS. After the last level is processed, `sum` holds the sum of the deepest leaves.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
from collections import deque

class Solution:
    def deepestLeavesSum(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return None
        q = deque()
        q.append(root)
        while q:
            sz = len(q)
            sum = 0
            for i in range(sz):
                cur = q.popleft()
                sum += cur.val
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
        return sum
```
