# 538. Convert BST to Greater Tree (把二叉搜索树转换为累加树)

**Difficulty:** Medium

## Problem

Given the `root` of a Binary Search Tree (BST), convert it to a Greater Tree such that every key of the original BST is changed to the original key **plus the sum of all keys greater than** the original key in the BST.

---

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.sum = 0

    def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        self.traverse(root)
        return root

    def traverse(self, root):
        if root is None:
            return
        self.traverse(root.right)
        self.sum += root.val
        root.val = self.sum
        self.traverse(root.left)
```

### 要点

**反序中序遍历（右 → 根 → 左）**  
普通中序（左 → 根 → 右）在 BST 上得到的是**升序**。反过来（先右再根再左），访问顺序就是**降序**。这样可以从大到小依次访问节点，维护一个「当前已访问节点值之和」的累加量，每到一个节点先累加再赋给 `root.val`，就得到「原值 + 所有比它大的值」的 Greater Tree。
