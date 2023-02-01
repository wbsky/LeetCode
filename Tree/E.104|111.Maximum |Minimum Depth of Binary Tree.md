### 题目链接
104. Maximum Depth of Binary Tree  https://leetcode.com/problems/maximum-depth-of-binary-tree/
111. Minimum Depth of Binary Tree  https://leetcode.com/problems/minimum-depth-of-binary-tree/

### 解题思路
* 递归
* BFS O(N)
* DFS O(N)

注意：最大深度和最小深度有所不同，最小深度需要判断是否为叶子节点、避免返回非叶子节点的最小深度，最大深度则不需要考虑返回中间节点的问题（中间节点不可能是最大深度）。

### Maximum Depth of Binary Tree
```Python
def maxDepth(self, root):
    if not root:
        return 0

    return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

### Minimum Depth of Binary Tree
```Python
def minDepth(self, root):
    if not root:
        return 0

    if (root.left) and (not root.right):
        return 1 + self.minDepth(root.left)

    if (root.right) and (not root.left):
        return 1 + self.minDepth(root.right)

    return 1 + min(self.minDepth(root.left), self.minDepth(root.right))

```
