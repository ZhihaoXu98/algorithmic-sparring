# 1110. Delete Nodes And Return Forest (删点成林)

**Difficulty:** Medium

## Problem

Given the root of a binary tree where each node has a distinct value, delete all nodes with values in a `to_delete` array. Return the roots of the remaining forest (disjoint trees).

## Constraints

- The number of nodes in the given tree will be in the range `[1, 1000]`.
- Each node has a distinct value between `1` and `1000`.
- `to_delete.length <= 1000`
- `to_delete` contains distinct values between `1` and `1000`.

---

## 我的思路

本指南旨在通过技术逻辑拆解，说明如何利用递归处理二叉树的结构变换问题。

### 第一阶段：算法策略 —— 为什么选择后序遍历？

**核心问题：** 在删除节点时，需要断开父节点与其的指针连接。如何最有效地实现这一点？

**遍历顺序：** 采用 **自底向上（后序遍历）**。

**逻辑依据：**

- 递归函数先处理左右子树，通过返回值将处理后的子树状态告知当前节点。
- 当前节点通过 `node.left = dfs(node.left)` 这种赋值方式，自动完成指针重连（断开或保持）。
- 这种方式避免了显式维护父节点引用的复杂性。

### 第二阶段：判定条件 —— 新根节点的产生逻辑

**判定逻辑：** 在森林中，一个节点被视为「新子树根节点」的必要条件是什么？

- **节点存活：** 当前节点不在 `to_delete` 集合中。
- **父节点被删除：** 当前节点的父节点被判定为需要删除。

**执行动作：** 当上述条件满足时，将该节点加入结果集 `res`。

### 第三阶段：递归函数（DFS）的定义与行为

递归函数 `dfs(node)` 的具体执行逻辑应遵循以下严格步骤：

1. **终止条件（Base Case）：** 若 `node` 为空，返回 `None`。
2. **递归下探：** 先执行 `node.left = dfs(node.left)` 和 `node.right = dfs(node.right)`。
3. **当前节点评估：**
   - **若当前节点在 `to_delete` 中：**
     - 检查已处理的子节点 `node.left` 和 `node.right`，若非空，则将其作为新根节点加入 `res`。
     - 返回 `None` 告知上层节点断开连接。
   - **若当前节点不在 `to_delete` 中：**
     - 返回 `node` 保持与上层节点的连接。

### 第四阶段：实现细节与边界处理

在编写代码时，需确保处理以下技术要点：

- **哈希查询优化：** 将 `to_delete` 列表转换为 `set`，将查询的时间复杂度从 $O(M)$ 降低至 $O(1)$。
- **空指针防御：** 在将子节点加入 `res` 之前，必须进行非空判定（`if node.left`），防止结果集中混入空值。
- **根节点（Root）特殊处理：** 由于初始根节点没有父节点，其存活状态无法通过「父节点被删」来判定。
  - **对策：** 接住 `dfs(root)` 的最终返回值，若不为空，手动将其加入 `res`。

### 第五阶段：核心代码实现

```python
class Solution:
    def delNodes(self, root: TreeNode, to_delete: List[int]) -> List[TreeNode]:
        self.to_delete_set = set(to_delete)
        self.res = []

        # 1. 执行递归，并接住处理后的初始根节点状态
        final_root = self.dfs(root)

        # 2. 判定初始根节点是否作为一棵树存活
        if final_root:
            self.res.append(final_root)

        return self.res

    def dfs(self, node: TreeNode) -> TreeNode:
        if not node:
            return None

        # 后序遍历：先处理子树
        node.left = self.dfs(node.left)
        node.right = self.dfs(node.right)

        # 处理当前节点
        if node.val in self.to_delete_set:
            if node.left:
                self.res.append(node.left)
            if node.right:
                self.res.append(node.right)
            return None  # 向上传递空值，断开连接

        return node  # 向上传递节点本身，保持连接
```

### 复杂度分析

- **时间复杂度：** $O(N)$。每个节点访问一次，哈希表查询为 $O(1)$。
- **空间复杂度：** $O(H + M)$。$H$ 为树的高度（递归栈空间），$M$ 为 `to_delete` 集合的大小。
