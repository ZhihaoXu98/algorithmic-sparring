# Lowest Common Ancestor (最近公共祖先)

---

## 1. 辅助函数：find

**定义：** 在以 `root` 为根的二叉树中寻找值为 `val` 的节点。

```python
def find(root: TreeNode, val: int) -> TreeNode:
    # base case
    if not root:
        return None
    # 看看 root.val 是不是要找的
    if root.val == val:
        return root
    # root 不是目标节点，那就去左子树找
    left = find(root.left, val)
    if left:
        return left
    # 左子树找不着，那就去右子树找
    right = find(root.right, val)
    if right:
        return right
    # 实在找不到了
    return None
```

---

## 2. 为什么不能写成「先算 left、right」并且**不写 return**？

**错误写法：**

```python
def find(root: TreeNode, val: int) -> TreeNode:
    if not root:
        return None
    if root.val == val:
        return root
    left = find(root.left, val)
    right = find(root.right, val)
    # 没有 return！
```

### 没有 return 会怎样？

在 Python 里，函数执行完所有语句却没有碰到 `return` 时，会**隐式返回 `None`**。

所以：就算在左子树里已经找到了目标节点（`left` 不是 `None`），你也没有把它「传回去」；当前层算完 `left` 和 `right` 之后什么都不 return，函数就返回 `None`。结果就是：**树里明明有目标节点，但 `find(root, val)` 永远得到 `None`**。

### 举例

树：找 `val = 4`（节点在左边）

```
        1
       / \
      2   3
     /
    4
```

- 在节点 4：`root.val == 4`，正确 `return root`，所以 `find(4, 4)` 返回节点 4。
- 回到节点 2：`left = find(2.left, 4)` 得到节点 4，`right = find(2.right, 4)` 得到 `None`。如果这里**不写 return**，函数结束，返回 `None`。于是 `find(2, 4)` 变成 `None`，而不是 4。
- 再回到节点 1：`left = find(2, 4)` 得到 `None`（因为 2 那一层没 return），最终 `find(1, 4)` 也是 `None`。

**结论：** 必须把「找到的结果」用 return 传上去。要么在左子树找到就 `return left`，要么最后写 `return left if left else right`；否则函数会错误地返回 `None`。

---

### 补充：如果写了 `return left if left else right`，但先算完 left、right 再 return 呢？

逻辑上可以拿正确答案，但会多递归：在左子树已经找到时，当前层仍会执行 `find(root.right, val)`，整棵右子树被多遍历一遍。所以**找到就立刻 return** 既能避免无效递归，又让语义更清晰。

---

## 3. 「后序位置」检查 root 的写法可以吗？

**写法：** 先递归左右子树，再在后序位置看 root 是不是目标，最后 `return left if left else right`。

```python
def find(root: TreeNode, val: int) -> TreeNode:
    if root is None:
        return None
    # 先去左右子树寻找
    left = find(root.left, val)
    right = find(root.right, val)
    # 后序位置，看看 root 是不是目标节点
    if root.val == val:
        return root
    # root 不是目标节点，再去看看哪边的子树找到了
    return left if left is not None else right
```

### 结论：逻辑上对，但效率差，不推荐

- **正确性：** 能返回正确结果：当前是目标就返回 root，否则返回左子树或右子树里找到的（若有）。
- **问题在于顺序：** 你在**后序**才判断 `root.val == val`，也就是说**每次都是先递归完左、右子树，再看当前节点**。这样没有任何「找到就停」的短路：
  - 目标在左子树时，你仍然会递归完整棵右子树。
  - 目标就是当前 root 时，你还是会先执行 `find(root.left, val)` 和 `find(root.right, val)`，再做 `root.val == val`，多两次递归。
- **结果：** 相当于做了一整棵树的**后序遍历**，时间复杂度固定是 $O(n)$，无法在找到目标后提前结束。

所以做「查找」时，更合适的还是**先看 root，再决定要不要进左/右**（即本文第 1 节的写法），这样找到就可以立刻 return，避免多余递归。

---

## LeetCode 236. Lowest Common Ancestor of a Binary Tree

**题意：** 给定二叉树和两个节点 `p`、`q`，求它们的最近公共祖先（LCA）。

### Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def __init__(self):
        self.alreadyfound = False

    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        return self.find(root, p.val, q.val)

    def find(self, root, p_val, q_val):
        if root is None:
            return None
        if self.alreadyfound:
            return None
        if root.val == p_val or root.val == q_val:
            return root
        left = self.find(root.left, p_val, q_val)
        right = self.find(root.right, p_val, q_val)

        if left and right:
            self.alreadyfound = True
            return root

        return left if left else right
```

### 为什么用 `alreadyfound`？

一旦在某一层发现 `left and right` 都不为空，说明当前 `root` 就是 LCA，我们设置 `self.alreadyfound = True` 并返回 `root`。之后在更上层的递归里，如果发现 `alreadyfound` 为真，就直接返回 `None`，不再继续往下或往旁支搜索。这样可以**避免在已经找到 LCA 之后，还去其他分支里「再找一个 LCA」**，保证返回的是第一次碰到的 LCA，并且提前终止后续递归。

### 是先看 root（前序）还是先看 left/right（后序）？

代码里**两种都有**，顺序是：

1. **先做一次前序判断**（第 134–135 行）：`if root.val == p_val or root.val == q_val: return root`。在递归到子树之前先看「当前节点是不是 p 或 q」。若是，直接返回 root。这样能正确处理「p 是 q 的祖先」（或反过来）的情况：先遇到的那个就是 LCA。
2. **再递归**得到 `left`、`right`。
3. **再做后序组合**：根据 `left` 和 `right` 决定返回谁——若两边都不为空则当前 root 是 LCA；否则返回非空的那一边。

所以：**「当前节点是不是 LCA」**必须后序做（需要先有 left、right）；**「当前节点是不是 p 或 q」**则在前序做，所以会先 check root 再递归。整体是「前序 check root → 递归 → 后序合并 left/right」。

**为什么 p、q 要前序判断？** 因为有可能 **p 或 q 本身就是 LCA**（即一个是另一个的祖先）。例如 p 是 q 的祖先：遍历时先到 p，此时还没递归，left/right 都未知。若没有前序的「root == p 或 q 就 return root」，我们会继续递归，最后只会在后序里得到「一边是 q、一边是 None」，于是 `return left if left else right` 会返回 q，而不是 p，就错了。所以前序看到当前节点就是 p 或 q 时立刻返回，才能正确把「作为 LCA 的 p 或 q」返回上去。

---

## LeetCode 1676. Lowest Common Ancestor of a Binary Tree IV

**题意：** 给定二叉树和一组节点 `nodes`（不止两个），求它们的最近公共祖先。

### Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:

    def lowestCommonAncestor(self, root: 'TreeNode', nodes: 'List[TreeNode]') -> 'TreeNode':
        vals = set()
        for node in nodes:
            vals.add(node.val)
        return self.find(root, vals)

    def find(self, root, vals):
        if root is None:
            return None
        if root.val in vals:
            return root
        left = self.find(root.left, vals)
        right = self.find(root.right, vals)
        if left and right:
            return root
        return left if left else right
```

### 为什么这里不能加 `alreadyfound`？

236 只有两个点，一旦出现「左右都不为空」的 root，它就是整题唯一的 LCA，可以设 `alreadyfound` 剪枝。

1676 有多个点。可能出现：当前 root 的左右子树各有一些目标节点，`left and right` 都非空，你误以为 root 就是答案并设了 `alreadyfound`，然后 return root。但此时这个 root 可能只是**一部分**目标节点的 LCA，**另一侧子树里还有别的目标节点**。例如 p、q 在左子树，r 在右子树：在左子树的 LCA（某个节点 A）处会得到 left、right 非空，若这里设 `alreadyfound = True` 并返回 A，之后递归到右子树时会直接因为 `alreadyfound` 而 return None，根本不会找到 r，最终会错误地返回 A；而真正包含 p、q、r 的 LCA 是更上面的节点。所以 1676 **不能**用 `alreadyfound`，必须把两侧都搜完，才能确定真正的 LCA。

### 为什么用 set 存 `vals`？

要频繁判断「当前节点值在不在目标节点集合里」：`root.val in vals`。用 list 存是 $O(n)$ 一次查询；用 set 是 $O(1)$，整道题只建一次 set，查询次数和节点数同量级，用 set 更合适。

---

## LeetCode 1644. Lowest Common Ancestor of a Binary Tree II

**题意：** 和 236 一样求 LCA，但 **p、q 可能不在树里**。若有一个不在，返回 `None`；否则返回 LCA。需要两个标志 `pfound`、`qfound`，最后若有一个没找到就返回 `None`。

### Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def __init__(self):
        self.pfound = False
        self.qfound = False

    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        res = self.find(root, p.val, q.val)
        if not self.pfound or not self.qfound:
            return None
        return res

    def find(self, root, p_val, q_val):
        if root is None:
            return None

        left = self.find(root.left, p_val, q_val)
        right = self.find(root.right, p_val, q_val)

        if root.val == p_val:
            self.pfound = True
            return root
        if root.val == q_val:
            self.qfound = True
            return root

        if left and right:
            return root

        return left if left else right
```

### 为什么必须后序：不能先看 root（前序）

**错误写法（前序）：** 一进来就先 `if root.val == p_val: ... return root`，再递归 left、right。

- 问题：一旦当前节点是 p（或 q），你就直接 return 了，**下面的 `left = self.find(...)` 根本不会执行**，整棵子树被跳过。
- 致命情况：p 是 q 的祖先（q 在 p 的子树里）。例如 p=5，q=3，且 3 是 5 的左孩子。前序先到 5，发现 5==p，立刻 return 5，不会去左子树找 3，q 永远找不到，`qfound` 一直是 False，最后返回 `None`。

**正确写法（后序）：** 先递归完 left、right，**再**看 root 是不是 p 或 q。

- 先搜完左右子树，再检查自己。这样即使 root 是 p，也不会提前 return，左子树里的 q 已经被 `find(root.left, ...)` 搜到并标记了 `qfound`，不会漏。
- 同一棵树：到 5 时先查左子树，在 3 处发现 q 并返回；再查右子树；最后再检查 5 是不是 p。两个都能被找到。

**一句话：** 前序「见到目标就 return」会短路，丢掉目标下面的另一个目标；后序「先搜完孩子再看自己」不会漏。
