# 222. Count Complete Tree Nodes (计算完全二叉树的节点数)

**Difficulty:** Medium

## Problem

Given the root of a **complete** binary tree, return the number of nodes in the tree.

完全二叉树：除最后一层外每层都满，最后一层从左到右连续填满（可能不满）。

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
    def countNodes(self, root: Optional[TreeNode]) -> int:
        l, r = root, root
        h_l = 0
        h_r = 0
        while l:
            l = l.left
            h_l += 1
        while r:
            r = r.right
            h_r += 1
        if h_l == h_r:
            return 2**h_l - 1
        else:
            return 1 + self.countNodes(root.left) + self.countNodes(root.right)
```

思路：从当前根沿最左路径得到高度 $h_l$，沿最右路径得到高度 $h_r$。若 $h_l == h_r$，当前子树是满二叉树，节点数为 $2^{h_l}-1$；否则递归左右子树并加 1（根）。

---

## 要点一：为什么左右子树一定有一个是满二叉树？

完全二叉树的定义：最后一层之前的层全部满，最后一层从左到右连续、无空位。

以当前根为界，把整棵子树按「根 → 左 / 根 → 右」分成左子树和右子树两棵子结构（都仍是完全二叉树）。最后一层节点要么还没越过「根的正下方」这条线（全在左半边），要么已经越过（左边全满，右边在填）。

- **若左子树高度 > 右子树高度**  
  说明最后一层还没延伸到右子树的最右，即右子树「最下面一层」根本没被用到。此时右子树是「少一层的满二叉树」（上面几层满，最下层不存在），所以右子树是满二叉树。

- **若左子树高度 == 右子树高度**  
  说明最后一层已经延伸到右子树的最右。完全二叉树从左到右连续填，要能填到右边，左边必须已经填满。所以此时左子树是满二叉树。

因此：每次递归时，**左、右两棵子树里至少有一棵是满二叉树**，可以直接用 $2^h-1$ 算节点数，不必再递归进去。

---

## 要点二：复杂度为什么是 $O(\log^2 N)$？

### 视角一：二分搜索的「工作量叠加」

可以把这个算法看成变种的二分查找。

- **普通二分查找**：每次砍掉一半，在剩下的一半里找。每次「砍」是 $O(1)$（比如看中间的数）。结论：层数 $\log N \times$ 每层工作量 $1 = O(\log N)$。
- **本算法**：也是每次砍掉一半（满二叉树的那一侧被直接算完、不再递归），但在「剩下的一半」上继续之前，必须先跑两个 `while` 量高度。量高度不是 $O(1)$，而是 $O(\text{当前树的高度})$，最大为 $\log N$。结论：层数 $\log N \times$ 每层最大工作量 $\log N = O(\log^2 N)$。

### 递归方程（Recurrence）严格推导

**1. 定义**

设树高为 $H$（完全二叉树有 $H = \log N$）。设 $T(H)$ 为处理一棵高度为 $H$ 的完全二叉树所需时间。

**2. 建立递推**

对 `countNodes(root)`：

- **算高度**：沿最左、最右走到底，耗时 $2 \cdot H$，即 $O(H)$。
- **若 $h_L = h_R$**（满）：直接返回 $2^h - 1$，总耗时 $O(H)$。
- **若 $h_L \neq h_R$**（非满）：递归左、右子树。由完全二叉树性质，一侧满、一侧非满：
  - **满的那侧**：进去后算高度 $O(H-1)$，发现相等后直接返回，该分支总耗时 $O(H)$。
  - **非满的那侧**：耗时 $T(H-1)$。

因此：

$$
T(H) = T(H-1) + O(H).
$$

**3. 展开求和**

$T(H) = T(H-1) + c \cdot H$，逐层展开：

$$
T(H) = T(0) + c \cdot (1 + 2 + \cdots + H) = c \cdot \frac{H(H+1)}{2}.
$$

故 $T(H) = O(H^2)$。

**4. 代入 $H = \log N$**

$$
T(N) = O\big((\log N)^2\big) = O(\log^2 N).
$$
