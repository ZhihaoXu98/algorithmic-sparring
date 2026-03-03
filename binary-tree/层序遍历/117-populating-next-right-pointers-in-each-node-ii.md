# 117. Populating Next Right Pointers in Each Node II (填充每个节点的下一个右侧节点指针 II)

**Difficulty:** Medium

## Problem

Given a binary tree, populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`. Initially, all next pointers are set to `NULL`.

---

## Solution

```python
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next

from collections import deque
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        q = deque()
        if root is None:
            return None
        q.append(root)
        while q:
            sz = len(q)
            pre = None
            for i in range(sz):
                cur = q.popleft()
                if pre:
                    pre.next = cur
                pre = cur
                if cur.left:
                    q.append(cur.left)
                if cur.right:
                    q.append(cur.right)        
        
        return root
```
