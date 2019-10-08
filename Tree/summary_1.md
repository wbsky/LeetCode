### 题目1：94. Binary Tree Inorder Traversal

题目链接: https://leetcode.com/problems/binary-tree-inorder-traversal/

问题：二叉树中序遍历

#### 解题思路
利用栈实现非递归的算法
#### 代码

```Python
def inorderTraversal(self, root):
    if not root:
        return []
    
    node, stack, ans = root, [], []
    while node or stack:
        if node:
            stack.append(node)
            node = node.left
        else:
            node = stack.pop()
            ans.append(node.val)
            node = node.right
    return ans
```
### 题目2：95. Unique Binary Search Trees II

题目链接: https://leetcode.com/problems/unique-binary-search-trees-ii/

问题：给定数组，给出所有可能的二叉搜索树

#### 解题思路
1. 递归，思考清楚每轮递归的返回结构 => **[TreeNode]**
2. 使用缓存，以避免重复计算
#### 代码

```Python
def generateTrees(self, n):
    if n < 1:
        return []
    
    memo = {}                        # 缓存，避免重复计算
    def core(a, b):
        if (a, b) in memo.keys():
            return memo[(a, b)]
        if a > b:                   # 若删除此句话则不可，尽管最后是return res or [None]，困惑？？
            return [None]
        if a == b:
            return [TreeNode(a)]
        
        res = []
        for i in range(a, b + 1):
            for l in core(a, i - 1):
                for r in core(i + 1, b):
                    root = TreeNode(i)
                    root.left, root.right = l, r
                    res.append(root)
        memo[(a, b)] = res
        return res or [None]
    
    return core(1, n)
```

### 题目3：96. Unique Binary Search Trees

题目链接: https://leetcode.com/problems/unique-binary-search-trees/

问题：计算可能的二叉搜索树的个数

#### 解题思路
动态规划，分别求解左右子树的个数，再计算总的个数即可
#### 代码

```Python
def numTrees(self, n):
    if n < 3:
        return n
    
    dp = [1 for i in range(n + 1)]
    dp[2] = 2
    for i in range(3, n+1):
        dp[i] = sum([dp[j]*dp[i-j-1] for j in range(0, i)])
    return dp[n]
```
### 题目4：98. Validate Binary Search Tree

题目链接: https://leetcode.com/problems/validate-binary-search-tree/

问题：验证是否为有效的二叉搜索树

#### 解题思路
验证思路：中序遍历为有序序列
#### 代码
```Python
# 递归求解中序遍历结果，再判断是否有效；需要对序列进行去重；时间复杂度过高
def isValidBST(self, root):
    def inorder(root):
        if not root:
            return []
        return inorder(root.left) + [root.val] + inorder(root.right)
    
    nodes = inorder(root)
    return len(nodes) == len(set(nodes)) and nodes == sorted(nodes)
```

```Python
# 二叉树非递归遍历，每次同之前遍历到的节点值进行比较
def isValidBST(self, root):
    stack, node, cur = [], root, None
    while stack or node:
        if node:
            stack.append(node)
            node = node.left
        else:
            node = stack.pop()
            if cur == None:     # 不可写成 if not cur，否则当cur为0时，判断有误
                cur = node.val
            else:
                if node.val <= cur:
                    return False
                cur = node.val
            node = node.right
    return True
```

### 题目5：99. Recover Binary Search Tree

题目链接: https://leetcode.com/problems/recover-binary-search-tree/

问题：找到二叉搜索树中不满足要求的两个节点并进行调整

#### 解题思路
二叉搜索树的中序遍历为有序序列，故需要找到两个节点，交换节点值，可使无序序列变为有序；在中序遍历过程中，搜索左右边界即可

#### 代码
```Python
def recoverTree(self, root):
    stack, order, node = [], [], root
    last, c1, c2 = None, None, None
    while node or stack:
        if node:
            stack.append(node)
            node = node.left
        else:
            node = stack.pop()
            if last and (not c1) and node.val < last.val: # 找到需要交换的左节点 
                c1, c2 = last, node
            if c1:                       # 左节点已找到的情况下，找到右节点 
                if node.val < c1.val:
                    c2 = node
                else:                    # 后面不可能有右节点，所以直接跳出
                    break
            last = node
            node = node.right
    c1.val, c2.val = c2.val, c1.val
```


### 题目6：100. Same Tree

题目链接: https://leetcode.com/problems/same-tree/

问题：判断两棵二叉树是否相同

#### 解题思路
递归，分别判断结构、节点值和左右子树即可
#### 代码

```Python
def isSameTree(self, p, q):
    if not p and not q:
        return True
    if p and q and p.val == q.val:
        return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
    return False
```

### 题目7：101. Symmetric Tree

题目链接: https://leetcode.com/problems/symmetric-tree/

问题：判断二叉树是否为镜像二叉树（本身与镜像相同）

#### 解题思路
1. 递归，对左右子树分别进行判断
2. 层序遍历，对每层元素分别进行镜像比照【不可仅比照元素，树的结构需要对称】
#### 代码

```Python
def isSymmetric(self, root):
    def core(l, r):
        if l and r and l.val == r.val:
            return core(l.left, r.right) and core(l.right, r.left)
        return l == r
    
    if not root:
        return True
    return core(root.left, root.right)
```
```Python
# 借助队列实现 【别人的代码~】
def isSymmetric(self, root):
    if not root:
        return True
    
    queue = collections.deque([root.left, root.right])
    while queue:
        a, b = queue.popleft(), queue.popleft()
        if not a and not b:
            continue
        elif a and b and a.val == b.val:
            queue.append(a.left)
            queue.append(b.right)
            queue.append(a.right)
            queue.append(b.left)
        else:
            return False
    return True
```

### 题目8：102. Binary Tree Level Order Traversal

题目链接: https://leetcode.com/problems/binary-tree-level-order-traversal/

问题：二叉树层序遍历

#### 解题思路
借助队列实现
#### 代码

```Python
def levelOrder(self, root):
    if not root:
        return []
    
    queue, ans = [root], []
    while queue:
        length = len(queue)
        tmp = []
        for i in range(length):
            node = queue.pop(0)
            tmp.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        ans.append(tmp)
    return ans
```
### 题目9：103. Binary Tree Zigzag Level Order Traversal

题目链接: https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/

问题：二叉树zigzap遍历

#### 解题思路
在层序遍历的基础上稍作修改，对每层加入元素的顺序/位置进行改变即可
#### 代码

```Python
def zigzagLevelOrder(self, root):
    if not root:
        return []

    queue, ans = [root], []
    mark = 0                     # 用于标记本层添加的元素是否逆序
    while queue:
        length = len(queue)
        tmp = []
        for i in range(length):
            node = queue.pop(0)
            tmp.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        if mark  == 1:
            tmp.reverse()       # 整体逆序
        ans.append(tmp)
        mark = 1- mark
    return ans
```

```Python
def zigzagLevelOrder(self, root):
    if not root:
        return []

    queue, ans = [root], []
    mark = 0
    while queue:
        length = len(queue)
        tmp = []
        for i in range(length):
            node = queue.pop(0)
            if mark == 0:
                tmp.append(node.val)
            else:
                tmp.insert(0, node.val)     # 更改插入的位置
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        ans.append(tmp)
        mark = 1- mark
    return ans
```

### 题目10：104. Maximum Depth of Binary Tree 【注意此题与T111（二叉树最小深度）的区别】

题目链接: https://leetcode.com/problems/maximum-depth-of-binary-tree/

问题：二叉树的最大深度

#### 解题思路
1. 递归
2. 层序遍历
#### 代码

```Python
# 递归
def maxDepth(self, root):
    if not root:
        return 0
    
    return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1
```

### 题目11：105. Construct Binary Tree from Preorder and Inorder Traversal

题目链接: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

问题：根据前序和中序遍历结果，重建二叉树

#### 解题思路
递归
#### 代码

```Python
# 我的代码 基础版
def buildTree(self, preorder, inorder):
    if not preorder or not inorder:
        return None
    
    r_val = preorder[0]
    inorder_idx = inorder.index(r_val)
    root = TreeNode(r_val)
    root.left = self.buildTree(preorder[1: 1+inorder_idx], inorder[:inorder_idx])
    root.right = self.buildTree(preorder[inorder_idx + 1:], inorder[inorder_idx + 1:])
    return root
```
```Python
# 参考他人,精简版代码
def buildTree(self, preorder, inorder):
    if inorder: 
        r_val = preorder.pop(0)
        inorder_idx = inorder.index(r_val)
        root = TreeNode(r_val)
        root.left = self.buildTree(preorder, inorder[:inorder_idx])
        root.right = self.buildTree(preorder, inorder[inorder_idx + 1:])
        return root
```

### 题目12：106. Construct Binary Tree from Inorder and Postorder Traversal

题目链接: https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

问题：根据后序和中序遍历结果，重建二叉树

#### 解题思路
递归
#### 代码

```Python
# 我的代码 基础版
def buildTree(self, inorder, postorder):
    if not postorder or not inorder:
        return None

    r_val = postorder[-1]
    inorder_idx = inorder.index(r_val)
    root = TreeNode(r_val)
    root.left = self.buildTree(inorder[:inorder_idx], postorder[: inorder_idx])
    root.right = self.buildTree(inorder[inorder_idx + 1:], postorder[inorder_idx: -1], )
    return root
```
```Python
# 参考他人,精简版代码
def buildTree(self, inorder, postorder):
    if inorder:
        r_val = postorder.pop()
        inorder_idx = inorder.index(r_val)
        root = TreeNode(r_val)
        # 注意，与前序遍历不同，此时要先生成右子树，再生成左子树，以和pop的顺序保持一致
        root.right = self.buildTree(inorder[inorder_idx + 1:], postorder)
        root.left = self.buildTree(inorder[:inorder_idx], postorder)
        return root
```
