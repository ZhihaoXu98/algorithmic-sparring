# 450. Delete Node in a BST

## Understanding

Same `root.left = recurse(...) / return root` pattern as insertion (701) — traverse to find the node, rewire on the way back up.

**Three cases when you find the node:**

- **No children / one child:** `if root.left is None: return root.right` handles both. When there are no children, `root.right` is also `None`, so it naturally returns `None`. One-child is just the same check returning the surviving child.
- **Two children:** the tricky case — use copy-and-delete.

**Two-children strategy — copy-and-delete:**
1. Find the in-order predecessor (go left once, then all the way right) — the largest value still smaller than the current node.
2. Copy its value into the current node.
3. Recursively delete the candidate from the left subtree.

No helper function needed — a simple `while temp.right` loop finds the candidate.

**Why not just rewire the candidate up?** The candidate still exists in its original position. You'd have a duplicate node and a broken tree:
```
    10                      7
   /  \     rewire →       / \
  5    15               5   15
 / \                   / \
3   7                 3   7  ← duplicate!
   /                     /
  6                     6
```

**Why a recursive call inside a recursive call?** The first recursion finds the node to delete. The second removes the candidate from the subtree. The candidate has at most one child (nothing to its right by definition), so the second call always hits the simple one-child/no-child case — just two passes down the tree.

**Takeaway:** When deletion requires a replacement, always copy the value then delete the original. Manually rewiring pointers is error-prone with complex subtrees.

## Solution

```python
class Solution:
    def deleteNode(self, root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
        if root is None:
            return None                          # key not found, nothing to delete

        if key > root.val:
            root.right = self.deleteNode(root.right, key)   # search right subtree
        elif key < root.val:
            root.left = self.deleteNode(root.left, key)     # search left subtree
        else:
            if root.left is None:
                return root.right                # no left child: replace with right (handles 0 and 1 child)
            if root.right is None:
                return root.left                 # no right child: replace with left
            # let us use the left subtree
            # find THAT guy lol
            temp = root.left
            while temp.right:
                temp = temp.right                # in-order predecessor: go left, then all the way right

            root.val = temp.val                  # copy predecessor's value into current node

            root.left = self.deleteNode(root.left, temp.val)  # delete the original predecessor node

            return root
        return root                              # not the target node — return self to parent
```

## Complexity

- **Time:** O(h) — first pass finds the target (O(h)), the `while` loop finds the predecessor (O(h)), second recursive call deletes it (O(h)); all sequential, still O(h). h = O(log n) balanced, O(n) worst.
- **Space:** O(h) — recursion call stack depth equals tree height.
