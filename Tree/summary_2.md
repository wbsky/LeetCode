### 题目1：107. Binary Tree Level Order Traversal II

题目链接: https://leetcode.com/problems/binary-tree-level-order-traversal-ii/

问题：将二叉树的每层元素保存到数组中

#### 解题思路
层序遍历，借助队列实现
#### 代码

```Python
def levelOrderBottom(self, root):
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
        ans = [tmp] + ans
    return ans
```

### 题目2：108. Convert Sorted Array to Binary Search Tree

题目链接: https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/

问题：将有序数组转换为BST

#### 解题思路
递归
#### 代码

```Python
def sortedArrayToBST(self, nums):
    if not nums:
        return None
    
    # 该处理在nums仅有1/2/3个节点的处理一致
    idx = len(nums)//2      
    root = TreeNode(nums[idx])
    root.left = self.sortedArrayToBST(nums[:idx])
    root.right = self.sortedArrayToBST(nums[idx+1:])
    return root
```
### 题目3：110. Balanced Binary Tree【值得仔细研究】

题目链接: https://leetcode.com/problems/balanced-binary-tree/

问题：判断二叉树是否为平衡二叉树

#### 解题思路
递归，针对每个节点，先判断左右子树是否平衡，再判断该节点是否平衡
#### 代码

```Python
def isBalanced(self, root):
    if not root:
        return True
    
    if not root.left and not root.right:
        return True
    
    def get_depth(root):
        if not root:
            return 0
        if not root.left and not root.right:
            return 1
        
        l_dep = get_depth(root.left)
        r_dep = get_depth(root.right)
        return max(l_dep, r_dep) + 1
    
    ans = True
    if root.left:
        ans = (ans and self.isBalanced(root.left))
    if root.right:
        ans = (ans and self.isBalanced(root.right))
    
    if not ans:             # 不加该句话则会超时！！！
        return ans
    
    l_dep, r_dep =  get_depth(root.left), get_depth(root.right)
    ans = ans and (False if abs(l_dep - r_dep) > 1 else True)
    return ans
```

```Python
# 优化版：将数的平衡性的判断与求解树深融合在一起，代码更为简洁
# 若子树不平衡则直接返回-1，否则返回子树高度
def isBalanced(self, root):

    def get_depth(root):
        if not root:
            return 0
        if not root.left and not root.right:
            return 1
        
        l_dep = get_depth(root.left)
        r_dep = get_depth(root.right)
        if l_dep == -1 or r_dep == -1:
            return -1
        if abs(l_dep - r_dep) > 1:
            return -1
        return max(l_dep, r_dep) + 1
    
    return (False if get_depth(root)  == -1 else True)
```

### 题目4：111. Minimum Depth of Binary Tree

题目链接: https://leetcode.com/problems/minimum-depth-of-binary-tree/

问题：二叉树的深度最小值

#### 解题思路
1. 递归
2. 层序遍历
#### 代码

```Python
def minDepth(self, root):
    if not root:
        return 0
    if not root.left and not root.right:        # 当前节点为叶子节点
        return 1
    
    # 若左子节点不存在，而右子节点存在，仅保留右子节点值即可；避免把非叶子节点当成叶子节点来计算深度！
    l_depth = 1 + self.minDepth(root.left) if root.left else float("inf")
    r_depth = 1 + self.minDepth(root.right) if root.right else float("inf")
    return min(l_depth, r_depth)
```

### 题目5：112. Path Sum

题目链接: https://leetcode.com/problems/path-sum/

问题：判断是否存在从根节点到叶子节点的节点之和为给定值

#### 解题思路
递归

#### 代码

```Python
def hasPathSum(self, root, sum):
    if not root:
        return False
    
    if not root.left and not root.right:
        return root.val == sum

    return self.hasPathSum(root.left, sum - root.val) or self.hasPathSum(root.right, sum - root.val)
```

### 题目6：113. Path Sum II

题目链接: https://leetcode.com/problems/path-sum-ii/

问题：输出从根节点到叶子节点的节点之和为给定值的路径

#### 解题思路
递归【注意边界条件】
1. 只有当**叶子节点**路径值为sum时，才加入路径
2. 左/右子树或不存在，需要进行判断弹出

#### 代码

```Python
# 法一：path为局部变量，每轮参与传参即可
def pathSum(self, root, sum):
    ans = []
    
    def dfs(root, path, sum):
        if not root:        # 左/右子树不存在时，直接返回即可
            return
        
        path, sum = path + [root.val], sum - root.val
        if not root.left and not root.right:  # 判断是否为叶子节点
            if sum == 0:
                ans.append(path)
            return
        
        dfs(root.left, path, sum)
        dfs(root.right, path, sum)
        return
    
    dfs(root, [], sum)
    return ans
```

```Python
# 法二：path为全局变量，需要随着迭代进行append和pop
def pathSum(self, root, sum):
    ans, path = [], []                        # path为全局变量
    
    def dfs(root, sum):
        if not root:
            return
        
        path.append(root.val) 
        sum -= root.val
        if not root.left and not root.right:  # 判断是否为叶子节点
            if sum == 0:
                ans.append(path[:])           # 此次必须赋值传递，否则后续会随path而改变 
            path.pop()                        # 若为叶子节点，进入该模块并执行完之后，进行pop; 无论sum如何，均需pop
            return
        
        dfs(root.left, sum)
        dfs(root.right, sum)
        path.pop()  # 非叶子节点，执行完子树运算后，进行pop
        return
    
    dfs(root, sum)
    return ans
```
### 题目7：114. Flatten Binary Tree to Linked List

题目链接: https://leetcode.com/problems/flatten-binary-tree-to-linked-list/

问题：将二叉树转换位单链表

#### 解题思路
1. 法一：保存前序遍历结果，然后再统一修改指针
2. 法二：递归，对每个子树的指针分别调转，直至完成整棵树的结构变换

#### 代码

```Python
def flatten(self, root):
    if not root:
        return
    
    self.flatten(root.left)
    self.flatten(root.right)
    
    right = root.right
    root.left, root.right = None, root.left     # 先连接上左子链表
    while root.right:           # 找到左子链表的最右端
        root = root.right
    root.right = right          # 将右端与右子链表相连接
```   
### 题目8：116. Populating Next Right Pointers in Each Node

题目链接: https://leetcode.com/problems/populating-next-right-pointers-in-each-node/

问题：为二叉树添加兄弟指针，输入二叉树为完美二叉树

#### 解题思路
1. 层序遍历，借助队列实现
2. 递归，逐渐修改子树节点即可
3. 借助两个指针，保留每层第一个节点和遍历到的位置

#### 代码

```Python
# 层次遍历 非递归，借助队列实现
def connect(self, root):
    if not root:
        return root
    
    queue = [root]
    while queue:
        length = len(queue)
        for i in range(length):     # 对于本层节点进行操作
            node = queue.pop(0)
            if i < length - 1:
                node.next = queue[0]
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return root
``` 
```Python
# 递归版 DFS
def connect(self, root):
    if not root:
        return root
    if not root.left:
        return root
    
    root.left.next = root.right
    root.right.next = root.next.left if root.next else None
    self.connect(root.left)
    self.connect(root.right)
    return root
```

```Python
# 进阶版 保留两个指针即可
def connect(self, root):
    if not root:
        return root
    
    start = root                    # 指针1：本层的开始节点
    while start and start.left:     # 只有当本层有左子节点时，再开始；每次均是对子树添加指针
        node = start
        while node:                 # 指针2：对本层的节点依次开始遍历
            node.left.next = node.right
            node.right.next = node.next.left if node.next else None
            node = node.next        # 此时，上层的连接已形成！
        start = start.left
    return root
```
### 题目9：117. Populating Next Right Pointers in Each Node II 【持续研究中…】

题目链接: https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/

问题：为二叉树添加兄弟指针

#### 解题思路
1. 层序遍历，借助队列实现，116的解法可直接运行通过
2. 利用dummy节点存储每行首个元素的指针

#### 代码

```Python
    def connect(self, root):
        if not root:
            return root

        head = root             # 保存，便于最后return
        start = Node(0)         # 头结点，next指针指向本行首个元素
        cur = start
        while root:
            if root.left:
                cur.next = root.left
                cur = cur.next
            if root.right:
                cur.next = root.right
                cur = cur.next
            root = root.next
            if not root:
                cur = start
                root = start.next   # 指针指向下一行首个元素
                start.next = None   # 遇到叶子节点时，不会进入两个个if，避免陷入死循环！！！
        return head
``` 
### 题目10：124. Binary Tree Maximum Path Sum

题目链接: https://leetcode.com/problems/binary-tree-maximum-path-sum/

问题：为二叉树添加兄弟指针

#### 解题思路
递归，针对每个节点同时计算出其最sum和包含该点的最大值路径

#### 代码

```Python
def maxPathSum(self, root):
    self.max_sum = float("-inf")                    # 初始化为负无穷（路径最大值或小于0）
    
    def core(root):
        if not root:
            return 0
        
        left_val = max(core(root.left), 0)
        right_val = max(core(root.right), 0)
        cur_sum = root.val + left_val + right_val   # 以该节点为路径拐点
        self.max_sum  = max(cur_sum, self.max_sum)  
        return root.val + max(left_val, right_val)  # 以该节点结尾的路径值
        
    core(root)
    return self.max_sum
``` 

### 题目11：129. Sum Root to Leaf Numbers

题目链接: https://leetcode.com/problems/sum-root-to-leaf-numbers/

问题：找到从根节点到叶子节点的所有路径值的和

#### 解题思路
递归遍历即可，注意细节【终止条件与返回值设置】

#### 代码

```Python
def sumNumbers(self, root):
    if not root:
        return 0
    
    def core(pre_sum, node):
        cur_val = node.val
        cur_sum = pre_sum * 10 + cur_val
        
        if not node.left and not node.right:        # 已到达叶子节点
            return cur_sum
        
        lsum, rsum = 0, 0                           
        # 后续或无左/右子树，以免无法return，故事先定义并赋值
        # 若右子树不存在，但左子树存在，右子树并非叶子，故记为0
        if node.left:
            lsum = core(cur_sum, node.left)
        if node.right:
            rsum = core(cur_sum, node.right)
        return lsum + rsum
        
    return core(0, root)
```

