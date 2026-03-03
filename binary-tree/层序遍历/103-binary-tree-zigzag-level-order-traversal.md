# 103. Binary Tree Zigzag Level Order Traversal (二叉树的锯齿形层序遍历)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, return the zigzag level order traversal of its nodes' values (i.e., from left to right, then right to left for the next level and alternate between).

---

## Key Insight

The key to the solution is to create a variable that controls at each level whether it goes left-to-right or right-to-left.

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
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        res = []
        q = deque()
        leftToRight = True
        if root is None:
            return []
        q.append(root)
        while q:
            sz = len(q)
            level = deque()
            for i in range(sz):
                cur = q.popleft()
                if leftToRight:
                    level.append(cur.val)
                else:
                    level.appendleft(cur.val)
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
            leftToRight = not leftToRight
            res.append(list(level))
        return res
```
