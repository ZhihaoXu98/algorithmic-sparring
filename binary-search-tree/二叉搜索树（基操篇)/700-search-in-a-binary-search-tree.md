# 700. Search in a Binary Search Tree

## Understanding

BST search exploits the BST invariant: left subtree values < node < right subtree values. At each node, you can eliminate half the tree by comparing `val` to `root.val`. The recursive solution is clean but uses O(h) stack space. The iterative version achieves the same O(h) time with O(1) space by just moving a pointer down the tree instead of making recursive calls.

## Solution 1 — Recursive

```python
class Solution:
    def searchBST(self,
root: Optional[TreeNode],
val: int) ->
Optional[TreeNode]:
        if root is None:          # base case: not found
            return None
        if val > root.val:        # target is in right subtree
            return self.searchBST(root.right, val)
        elif val < root.val:      # target is in left subtree
            return self.searchBST(root.left, val)
        else:
            return root           # found it
```

### Complexity
- **Time:** O(h) — visit one node per level, h = tree height (O(log n) balanced, O(n) worst)
- **Space:** O(h) — recursion call stack depth equals tree height

## Solution 2 — Iterative (more efficient)

```python
class Solution:
    def searchBST(self,
root: Optional[TreeNode],
val: int) ->
Optional[TreeNode]:
        while root and root.val != val:          # stop when found or exhausted
            root = root.right if val > root.val else root.left  # move right or left
        return root                              # None if not found, node if found
```

### Complexity
- **Time:** O(h) — same traversal path as recursive
- **Space:** O(1) — no call stack, just a pointer moving down the tree
