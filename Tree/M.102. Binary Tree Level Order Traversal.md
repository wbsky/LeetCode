题目链接：https://leetcode.com/problems/binary-tree-level-order-traversal/

### 解题思路
* BFS：各层依次遍历，存入对应数组
* DFS：记录遍历到的层，并根据所在层将val记录到对应的层数据中，不够直观

### 代码
```Python
# BFS
class Solution(object):
    def levelOrder(self, root):
        if not root:
            return []
        
        res, cur = [], [root]
        while cur:
            level, nxt = [], []
            for p in cur:
                level.append(p.val)
                if p.left:
                    nxt.append(p.left)
                if p.right:
                    nxt.append(p.right)
            res.append(level)
            cur = nxt
            
        return res

# DFS
class Solution(object):
    def levelOrder(self, root):
        def dfs(root, level):
            if not root:
                return 
            
            if len(res) > level:
                res[level].append(root.val)
            else:
                res.append([])
                res[level].append(root.val)
            dfs(root.left, level + 1)
            dfs(root.right, level + 1)
            
        if not root:
            return []
        
        res = []
        dfs(root, 0)
        return res
        
        

```
