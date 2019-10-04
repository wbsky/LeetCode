### 题目1：257. Binary Tree Paths

题目链接: https://leetcode.com/problems/binary-tree-paths/

问题：找到从根节点到叶子节点的所有路径

#### 解题思路
DFS
* 当用列表（**引用传递，始终指向同一个列**表）保存中间路径时，再回溯时，要记得pop()出已添加元素
* 若用一般的变量 or 字符串（**值传递，每轮调用过程中变量改变**）保存中间路径，调用过程则会自动保存当前路径


#### 代码

```Python
def binaryTreePaths(self, root):
    if not root:
        return []
    
    ans = []
    def find_path(node, path):
        if not node.left and not node.right:
            path.append(node)
            ans.append("->".join([str(node.val) for node in path]))
            path.pop()          # 及时pop
            return
        
        if node.left:
            path.append(node)
            find_path(node.left, path)
            path.pop()          # 及时pop
        if node.right:
            path.append(node)
            find_path(node.right, path)
            path.pop()          # 及时pop
        
    find_path(root, [])
    return ans
```

```Python
def binaryTreePaths(self, root):
    if not root:
        return []
    
    ans = []
    def find_path(node, path):
        path += ("->" + str(node.val))
        if not node.left and not node.right:
            ans.append(path[2:])        # 根节点前面多一个"->"
            return
        
        if node.left:
            find_path(node.left, path)               
        if node.right:
            find_path(node.right, path)
        
    find_path(root, '')
    return ans
```
#

### 题目2：236. Lowest Common Ancestor of a Binary Tree

题目链接: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

问题：求二叉树中两个节点最近的公共父节点

#### 解题思路
1. 对两个节点分别进行深度优先遍历，保存根节点到该节点的路径序列，对两个序列进行比对即可 => 此方法需要对整棵树遍历两遍
2. 自顶向上搜索，对二叉树的每个节点
    * p或q等于该节点，则该节点为最近父节点
    * p和q均位于左子树
    * p和q一个位于左子树，一个位于右子树
    
所以，每搜索一个节点，我们对该节点及其子树的情况进行记录，故需要DFS。
如果找到了这个node，就会return这个node的pointer，所以只要return的left或right不是NULL，就意味在left或right找到了either p or q

* 如果left和right都不为空，说明q和p分布在左右两个branch
* 如果left和right都空，说明p和q不存在于树中
* 如果只有一个为空，说明p和q在同一侧【因为返回的那个非NULL值肯定是p和q中的一个，而且另一个肯定在这个返回的node的下面，所以直接return这个返回的值】

#### 代码
```Python
def lowestCommonAncestor(self, root, p, q):
    if not root or  root == p or root == q:
        return root
        
    left = self.lowestCommonAncestor(root.left, p, q)
    right = self.lowestCommonAncestor(root.right, p, q)
    if left and right:
        return root
    
    if not left:
        return right
    else:
        return left
```
#

###  题目3：235. Lowest Common Ancestor of a Binary Search Tree

题目链接: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

问题：求**二叉搜索树**中两个节点最近的公共父节点

#### 解题思路
由于二叉搜索树节点有序，故可直接采用二分的方法求解。逐级判断两个节点与当前节点值之间的关系即可

#### 代码
```Python
def lowestCommonAncestor(self, root, p, q):
    max_num, min_num = max(p.val, q.val), min(p.val, q.val)
    cur_node = root
    while True:
        if cur_node.val == max_num or cur_node.val == min_num:
            return cur_node
        
        if cur_node.val > max_num:
            cur_node = cur_node.left
        elif cur_node.val < min_num:
            cur_node = cur_node.right
        else:
            return cur_node
```

#

###  题目4：230. Kth Smallest Element in a BST

题目链接: https://leetcode.com/problems/kth-smallest-element-in-a-bst/

问题：求**二叉搜索树**中第k小的元素

#### 解题思路
1. 二叉搜索树中序遍历即可得到有序序列，保存前k个元素即可，不必完成全部遍历。
2. 二叉树非递归中序遍历，扫描至第k个元素即可

#### 代码
```Python
def kthSmallest(self, root, k):
    tmp = []
    def core(node):
        if len(tmp) > k:   # 找到前k个元素即可停止遍历
            return
        if node.left:
            core(node.left)    
        tmp.append(node.val)
        if node.right:
            core(node.right)
    core(root)   
    return tmp[k-1]
```

```Python
def kthSmallest(self, root, k):
    stack = []
    while stack or root:
        if root:
            stack.append(root)
            root = root.left
        else:
            node = stack.pop()
            k -= 1
            if k == 0:
                return node.val
            root = node.right
```


#

###  题目5：226. Invert Binary Tree

题目链接: https://leetcode.com/problems/invert-binary-tree/

问题：将每个节点的左子节点和右子节点对调，生成新的数。

#### 解题思路
DFS，分别对调每个节点的子节点即可

#### 代码
```Python
# 自己的代码，还是不够精简
def invertTree(self, root):
    def core(node):
        if not node:
            return 
        if node.left or node.right:    # 有可能仅有一侧有值，但仍需对换
            node.right, node.left = node.left, node.right
        core(node.left)
        core(node.right)
        
    core(root)
    return root
```
```Python
def invertTree(self, root):
    if root:
        root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
    return root
```

###  题目6：222. Count Complete Tree Nodes

题目链接: https://leetcode.com/problems/count-complete-tree-nodes/

问题：统计完全二叉树(前h-1层均为满的，最后一层节点左排)中的节点个数

#### 解题思路
1. 遍历统计个数
2. 先判断是否为满二叉树，若是的话，则可直接计算个数；若否，则对左右子树分别计算即可

#### 代码
```Python
# 法一：遍历统计个数
def countNodes(self, root):
    if not root:
        return 0
    return 1 + self.countNodes(root.left) + self.countNodes(root.right)
```

```Python
# 法二：通过左右路径长度判断是否为满二叉树，再继续计算
    def countNodes(self, root):
        if not root:
            return 0
        
        node, lh = root, 0
        while node:
            lh += 1
            node = node.left
        
        node, rh = root, 0
        while node:
            rh += 1
            node = node.right
                    
        if lh == rh:
            return 2**lh - 1
        else:
            return 1 + self.countNodes(root.left) + self.countNodes(root.right)
```
```Python
# 法二改进版：一次遍历，判断左右路径是否等长
    def countNodes(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return 0
        
        lnode, rnode, h = root, root, 0
        while rnode:
            h += 1
            rnode = rnode.right
            lnode = lnode.left
         
        if not lnode:
            return 2**h - 1
        else:
            return 1 + self.countNodes(root.left) + self.countNodes(root.right)
```

###  题目7：199. Binary Tree Right Side View

题目链接: https://leetcode.com/problems/binary-tree-right-side-view/

问题：返回二叉树的右侧视图，即每层的最后一个节点值

#### 解题思路
层序遍历
* 利用队列实现，保存每层的最后一个元素即可
* 利用栈实现，每次从栈顶取出，判断是否为新的一层，若是的话，则必为新的一层的最后一个元素

#### 代码
```Python
# 利用队列实现
def rightSideView(self, root):
    if not root:
        return []
    
    queue = [[root, 1]]
    last_layer, ans = 0, []
    while queue:
        top, cur_layer = queue.pop(0)
        if cur_layer > last_layer:
            ans.append(top.val)
            last_layer = cur_layer
        else:
            ans[cur_layer-1] = top.val      # 不断更新本层元素
        
        if top.left:
            queue.append([top.left, cur_layer + 1])
        if top.right:
            queue.append([top.right, cur_layer + 1])
    return ans
```
```Python
# 利用栈实现
def rightSideView(self, root):
    if not root:
        return []
    
    stack = [[root, 1]]
    last_layer, ans = 0, []
    
    while stack:
        top, cur_layer = stack.pop()
        if cur_layer > last_layer:      # 此时取出的必为新的一层最右侧的元素
            ans.append(top.val)
            last_layer = cur_layer
        if top.left:
            stack.append([top.left, cur_layer + 1])
        if top.right:
            stack.append([top.right, cur_layer + 1])
    return ans
```

###  题目8：173. Binary Search Tree Iterator【持续研究中…】

题目链接: https://leetcode.com/problems/binary-search-tree-iterator/

问题：实现二叉搜索树的迭代器，要求：时间复杂度 O(1), 空间复杂度 O(h)

#### 解题思路
二叉树的非递归中序遍历，【注意逐渐继续添加元素的逻辑】

#### 代码
```Python
class BSTIterator(object):
    def __init__(self, root):
        self.stack, node = [], root
        while node:
            self.stack.append(node)
            node = node.left
        
    def next(self):                 # 返回最小的元素，同时加入后续递增元素（右子树上的左侧值）
        node = self.stack.pop()
        cur_node = node.right
        while cur_node:
            self.stack.append(cur_node)
            cur_node = cur_node.left
        return node.val
        
    def hasNext(self):
        return len(self.stack) > 0
```

###  题目9：145. Binary Tree Postorder Traversal

题目链接: https://leetcode.com/problems/binary-tree-postorder-traversal/

问题：二叉树非递归后序遍历

#### 解题思路
二叉树的非递归后序遍历

#### 代码
```Python
def postorderTraversal(self, root):
    if not root:
        return []

    stack, ans, node = [], [], root
    while node or stack:
        if node:
            stack.append([node, False])     # 保存标志位
            node = node.left
        else:
            if stack[-1][1] == False:       # 仅遍历了左子树，所以需访问右子树
                stack[-1][1] = True
                node = stack[-1][0].right
            else:                           # 右子树也已完成遍历
                ans.append(stack.pop()[0].val)
    return ans
```

###  题目10：144. Binary Tree Preorder Traversal

题目链接: https://leetcode.com/problems/binary-tree-preorder-traversal/

问题：二叉树非递归前序遍历

#### 解题思路
二叉树的非递归中序遍历，【注意逐渐继续添加元素的逻辑】

#### 代码
```Python
def preorderTraversal(self, root):
    if not root:
        return []
    
    stack, ans, node = [], [], root
    while node or stack:
        if node:
            stack.append(node)
            ans.append(node.val)
            node = node.left
        else:
            node = stack.pop().right
    return ans
```
