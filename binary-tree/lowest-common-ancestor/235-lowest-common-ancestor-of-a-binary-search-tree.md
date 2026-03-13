# 235. Lowest Common Ancestor of a Binary Search Tree (BST 的最近公共祖先)

**Difficulty:** Easy

## Problem

Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes p and q in the BST.

**Note:** 利用 BST 特性：左子树都小于根，右子树都大于根。不需要像 236 那样后序合并，也不用先给 p、q 比大小。

---

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        return self.find(root, p.val, q.val)

    def find(self, root, p_val, q_val):
        if root is None:
            return None
        if p_val < root.val and q_val < root.val:
            return self.find(root.left, p_val, q_val)
        if p_val > root.val and q_val > root.val:
            return self.find(root.right, p_val, q_val)
        return root
```

### 思路

- 若 **p、q 都小于 root**：LCA 一定在左子树，往左走。
- 若 **p、q 都大于 root**：LCA 一定在右子树，往右走。
- 否则（一个 ≤ root，一个 ≥ root，或有一个等于 root）：当前 root 就是 LCA，直接返回。

不需要先比较 p、q 谁大谁小；两条 `if` 已经覆盖「同侧」情况，剩下的就是 LCA。
