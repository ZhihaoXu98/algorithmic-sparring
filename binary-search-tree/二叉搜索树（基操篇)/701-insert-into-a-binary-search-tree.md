# 701. Insert into a Binary Search Tree

## Understanding

Insertion follows the same traversal as BST search (700): go left if smaller, go right if larger. The only difference is what happens at `None` — instead of returning "not found," you create and return a new node there.

**`return TreeNode(val)`:** Reaching `None` means you've found the correct empty slot. You create the node and return it up to the caller, which is a parent doing `root.left = recurse(...)` or `root.right = recurse(...)`. That assignment is how the new node attaches to the tree.

**`return root`:** Every non-insertion node must return itself so its parent can re-link it. Without this, `root.left = recurse(...)` would receive `None` and you'd lose the entire subtree. Each node is essentially saying "nothing changed here, I'm still your child."

**`root.left = recurse(root.left, ...)` pattern:** A fundamental tree-modification technique. Each level connects the result from below, keeping the tree structure intact as recursion unwinds. Reappears in delete, rebuild, and other tree problems.

The insertion point as a leaf is not the only valid answer — multiple valid BSTs can result — but it's the simplest since it requires no restructuring.

## Solution

```python
class Solution:
    def insertIntoBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        if root is None:
            return TreeNode(val)                          # found the empty slot — create and return new node
        if val > root.val:
            root.right = self.insertIntoBST(root.right, val)  # recurse right; re-link whatever comes back
        elif val < root.val:
            root.left = self.insertIntoBST(root.left, val)    # recurse left; re-link whatever comes back

        return root                                       # nothing changed here — return self to parent
```

## Complexity

- **Time:** O(h) — follows one root-to-leaf path, h = tree height (O(log n) balanced, O(n) worst)
- **Space:** O(h) — recursion call stack depth equals tree height
