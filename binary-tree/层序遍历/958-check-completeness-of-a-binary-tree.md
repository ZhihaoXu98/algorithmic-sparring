# 958. Check Completeness of a Binary Tree (二叉树的完全性检验)

**Difficulty:** Medium

## Problem

Given the root of a binary tree, determine if it is a complete binary tree.

---

## Key Idea

The trick is to treat **None (null)** as actual elements in the queue.

1. **Traverse:** Standard BFS with a queue.
2. **The pivot point:** As soon as you pull a `None` out of the queue, you have reached the point where the tree should end.
3. **The check:** If you see any more real nodes after that first `None`, the tree is not complete — there is a "gap" or a node too far to the right.

If the last layer is perfect, the queue will eventually be full of `None`s; the loop just `continue`s until the queue is empty.

---

## Solution

```python
from collections import deque

class Solution:
    def isCompleteTree(self, root: Optional[TreeNode]) -> bool:
        # A flag to track if we've encountered the first "empty" spot
        seen_none = False
        
        # Initialize queue with the root to start the BFS
        q = deque([root])
        
        while q:
            # Level-order traversal: process nodes in the order they appear
            sz = len(q)
            for i in range(sz):
                cur = q.popleft()
                
                # If the current node is null, we've hit a potential end/gap
                if cur is None:
                    seen_none = True
                    continue
                
                # If we encounter a real node AFTER seeing a None,
                # it means there was a gap to its left.
                # This violates the "Complete Binary Tree" definition.
                if seen_none:
                    return False
                
                # Add children to the queue.
                # We MUST add None children to detect gaps in the next level.
                q.append(cur.left)
                q.append(cur.right)
                
        # If we finished the BFS without finding a gap, it's a complete tree.
        return True
```
