# 1609. Even Odd Tree (奇偶树)

**Difficulty:** Medium

## Problem

A binary tree is named **Even-Odd** if it satisfies:

- At level 0 (root), all nodes have **odd** values, in **strictly increasing** order (left to right).
- At level 1, all nodes have **even** values, in **strictly decreasing** order.
- At level 2, odd again, strictly increasing, and so on.

Return `true` if the binary tree is Even-Odd, otherwise `false`.

---

## Solution

Nothing to note — just be careful about the two constraints (parity and strict monotonicity per level). Read the question carefully. Simple BFS.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
from collections import deque
class Solution:
    def isEvenOddTree(self, root: Optional[TreeNode]) -> bool:
        if root is None:
            return True
        q = deque()
        level = 0
        q.append(root)
        while q:
            sz = len(q)
            even = (level % 2 == 0)
            prev = float('-infinity') if even else float('infinity')
            for i in range(sz):
                cur = q.popleft()
                if even:
                    if cur.val <= prev or cur.val % 2 != 1:
                        return False
                else:
                    if cur.val >= prev or cur.val % 2 != 0:
                        return False

                prev = cur.val
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)
            level += 1
        return True
```
