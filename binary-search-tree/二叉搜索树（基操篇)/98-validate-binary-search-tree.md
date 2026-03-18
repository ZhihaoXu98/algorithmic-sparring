# 98. Validate Binary Search Tree

## Problem

Given the root of a binary tree, determine if it is a valid BST.

A valid BST requires:
- Left subtree contains only nodes with values **less than** the node's value
- Right subtree contains only nodes with values **greater than** the node's value
- Both subtrees must also be valid BSTs

## Why the Naive Approach Fails

Checking each node's immediate children isn't enough. A subtree can be locally valid but globally invalid:

```
    5
   / \
  1   4
     / \
    3   6
```

Node 4's subtree looks like a valid BST, but 4 < 5, so the whole tree is invalid.

## Key Insight: Range Narrowing

Every node has a **valid range** determined by its ancestors. As you traverse deeper, the range gets tighter:

- Root: `(-inf, +inf)` — no constraints
- Going **left**: current node's value becomes the new **upper bound**
- Going **right**: current node's value becomes the new **lower bound**

At each node, check: does `lower_bound < node.val < upper_bound`?

## Solution

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        return self._isValid(root, float('-inf'), float('inf'))

    def _isValid(self, root, lower_bnd, upper_bnd):
        if root is None:
            return True
        if root.val <= lower_bnd or root.val >= upper_bnd:
            return False
        return (self._isValid(root.left, lower_bnd, root.val) and
                self._isValid(root.right, root.val, upper_bnd))
```

## Complexity

- **Time:** O(N) — visit every node once
- **Space:** O(H) — recursion stack, where H is tree height

## Takeaway

When a constraint depends on **all ancestors**, not just the parent, pass accumulated constraints down as parameters in your recursive function. This **range narrowing** pattern appears in other tree problems too (e.g., constructing BSTs from preorder traversal).
