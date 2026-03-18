# 96. Unique Binary Search Trees

## Understanding

**Try each root:** For each `i` from 1 to n, make it the root. Left subtree gets `i - 1` nodes, right subtree gets `n - i` nodes. Multiply left count × right count, sum over all choices of `i`.

**Only count matters, not values:** `count(1,2)` and `count(4,5)` produce the same number of shapes — BST structure depends only on how many nodes, not which values. So the whole problem reduces to `count(n)`.

**Why multiply:** Left and right subtree shapes are independent choices. With 2 left shapes and 2 right shapes, you get 2 × 2 = 4 total combinations for that root. Summing across all roots gives the total.

Example — n = 5, root = 3:
```
        [3]  ← root
       /   \
  {1, 2}   {4, 5}
```
Left has 2 shapes (A, B), right has 2 shapes (C, D) → 4 combinations:
```
A + C       A + D       B + C       B + D

   3           3           3           3
  / \         / \         / \         / \
 1   4       1   5       2   4       2   5
  \   \       \  /       /    \      /  /
   2   5       2 4      1      5    1  4
```

**Base case:** Both `n = 0` and `n = 1` return 1. An empty tree is one valid configuration. Crucially, `n = 0` must return 1 (not 0) — otherwise the multiplication zeroes out every term that has an empty subtree.

**Memoization:** Plain recursion recomputes `count(3)`, `count(2)`, etc. from many different call sites. Cache results in an array on first computation, look them up on subsequent calls. Drops time from exponential to O(n²). Initialize the memo inside `numTrees` (where `n` is known), then share it via `self.memo`.

**Broader takeaway:** Memoization = top-down DP. Same table, built on demand instead of bottom-up. Any recursive solution with overlapping subproblems is a candidate.

## Solution

```python
class Solution:
    def numTrees(self, n: int) -> int:
        self.memo = [0] * (n+1)    # memo[i] = number of unique BSTs with i nodes; 0 means uncomputed
        return self.count(n)

    def count(self, n):
        if n <= 1:
            return 1               # base case: 0 nodes (empty tree) or 1 node = exactly 1 BST
        if self.memo[n] != 0:
            return self.memo[n]    # already computed — return cached result
        res = 0
        for i in range(n):         # i = number of nodes in left subtree (0 to n-1)
            res += (self.count(i) * self.count(n-i-1))  # left shapes × right shapes for this root
        self.memo[n] = res         # cache before returning
        return res
```

## Complexity

- **Time:** O(n²) — There are n+1 unique subproblems: `count(0)` through `count(n)`, each computed only once. For each `count(k)`, the loop runs k iterations. Total work: 0 + 1 + 2 + ... + n = n(n+1)/2 = O(n²).
- **Space:** O(n) — The memo array stores n+1 values (O(n)). The recursion stack goes at most n levels deep (`count(n)` → `count(n-1)` → ... → `count(0)`), also O(n).
