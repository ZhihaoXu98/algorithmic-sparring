# 95. Unique Binary Search Trees II

## Understanding

Same idea as 96 — try every node as root, recurse on left and right intervals — but instead of counting, you enumerate. Each call returns a list of all valid subtree roots for the interval `[lo, hi]`, then you combine them with nested loops.

**Why `[None]` not `[]` or `None` for the base case:**

The base case must play nicely with the combination loops:
```python
for left in leftTrees:
    for right in rightTrees:
        # build tree
```
- `[]` → loop runs zero times → you silently drop trees that have one empty side
- `None` → can't iterate → crash
- `[None]` → loop runs once with `left=None` or `right=None` → correctly builds a node with an empty child

**Takeaway:** When recursion returns a list that gets iterated over, the "empty" result must be `[None]`, not `[]`.

**Count vs. enumerate — the mental shift:**

| Problem 96 | Problem 95 |
|---|---|
| "How many?" | "Which ones?" |
| Returns `int` | Returns `List[TreeNode]` |
| Combine with `*` and `+` | Combine with nested loops |

When a problem asks you to enumerate instead of count, your recursive function returns a list of possibilities, and you combine lists with iteration rather than arithmetic.

## Solution

```python
class Solution:
    def generateTrees(self, n: int) -> List[Optional[TreeNode]]:
        return self.build(1, n)

    def build(self, lo, hi):
        res = []
        if lo > hi:
            return [None]                        # empty interval = one valid empty subtree
        for i in range(lo, hi+1):               # try each value as root
            leftTree = self.build(lo, i-1)       # all valid left subtrees
            rightTree = self.build(i+1, hi)      # all valid right subtrees
            for left in leftTree:
                for right in rightTree:          # pair every left shape with every right shape
                    root = TreeNode(i)
                    root.left = left
                    root.right = right
                    res.append(root)             # each (left, right) combo is one unique BST
        return res
```

## Complexity

The number of unique BSTs with n nodes is the nth Catalan number: C(n) ~ 4ⁿ / (n^(3/2) · √π).

- **Time:** O(n · C(n)) — C(n) trees are generated, each requiring O(n) work to construct (one node per level of the tree).
- **Space:** O(n · C(n)) — to store all C(n) trees, each with n nodes. The recursion stack is O(n) deep but dominated by the output size.

**Interview tip:** You don't need to memorize "Catalan number" or the exact formula. It's enough to say:

> "The number of unique BSTs grows exponentially. Each tree has n nodes, so total work is roughly (number of trees) × n. It's at least exponential but not exactly factorial."

If pressed, mention it's related to Catalan numbers. Interviewers care more that you reason through complexity than that you nail the exact formula — walking through your loops out loud is better than guessing.
