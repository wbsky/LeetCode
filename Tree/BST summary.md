### 题目1：450. Delete Node in a BST

题目链接: https://leetcode.com/problems/delete-node-in-a-bst/

问题：删除二叉搜索树中的节点

#### 解题思路
递归，分情况讨论
* 待删除节点无左右子树 => 直接剔除即可
* 待删除节点仅有左子树或仅有右子树 => 直接把有的子树接上就行
* 待删除节点p既有左子树，又有右子树
    * 将左子树上的最大值p1（最右节点）或右子树的最小值p2（最左节点）赋予待删除节点
    * 以左子树最大值p1为例，**最大值p1赋予到待删除位置后，可能剩余[p1-左子树] p1.left，需要连接到已上移的节点位置**

#### 代码
```Python
def deleteNode(self, root: TreeNode, key: int) -> TreeNode:
    if not root:
        return root
    
    if root.val == key:
        if not root.left:
            return root.right
        elif not root.right:
            return root.left
        else:
            p = root.left
            prev = root
            while p.right:
                prev = p
                p = p.right             # 找到左子树的最右节点
            root.val = p.val
            
            if prev == root:           # 将剩余的子树拼接到原树上，此处需注意不同的拼接状况
                prev.left = p.left
            else:
                prev.right = p.left
            return root

    if root.val < key:
        root.right = self.deleteNode(root.right, key)
        
    if root.val > key:
        root.left = self.deleteNode(root.left, key)
        
    return root

```


```Python
"""
    改进版：删除子树节点时，递归操作即可
"""
def deleteNode(self, root: TreeNode, key: int) -> TreeNode:

    if not root:
        return None
        
    if root.val > key:
        root.left = self.deleteNode( root.left, key )
        
    elif root.val < key:
        root.right = self.deleteNode( root.right, key )

    else:
        if (not root.left) or (not root.right):
            root = root.left if root.left else root.right
        else:
            cur = root.left            
            while cur.right:
                cur = cur.right                             # 找到左子树的最右节点
            root.val = cur.val                              # 进行替换
            root.left = self.deleteNode(root.left, cur.val)  # 递归对子树进行调整即可
        
    return root

```
##

### 题目2：449. Serialize and Deserialize BST

题目链接: https://leetcode.com/problems/serialize-and-deserialize-bst/

问题2：二叉搜索树的序列号与反序列化

#### 解题思路
##### 知识点
* 二叉搜索树的**中序遍历**为**有序序列**
* 由二叉搜索树的**前序/后序遍历**可以直接复原得到原树
* 一般的二叉树需要 前序+中序 或 后序+中序 才可复原二叉树

#### 代码
```Python
class Codec:

    def serialize(self, root: TreeNode) -> str:
        
        def core(node):           # 前序遍历
            if node:
                return [node.val] + core(node.left) + core(node.right)
            return []
            
        ods = [str(item)  for item in core(root)] 
        return "#".join(ods)      # 序列化     # 用map优化：'#'.join(map(str, ods))
        

    def deserialize(self, data: str) -> TreeNode:
        if not data:
            return None
        
        def core(ods):           # 序列解析
            if not ods:
                return None
            
            root = TreeNode(ods[0])
            bnd = len(ods)     # 子树为空时的默认选择
            for i in range(1, len(ods)):
                if ods[i] > ods[0]:
                    bnd = i
                    break        
            
            root.left = core(ods[1:bnd])
            root.right = core(ods[bnd:])
            return root
    
        ods = [int(item) for item in data.split('#')]
        return core(ods)
```

#### 算法提升
* 利用边界值设定+队列（不知道这么叫是不是合适），实现递归复原

https://leetcode.com/problems/serialize-and-deserialize-bst/discuss/93171/Python-O(-N-)-solution.-easy-to-understand

* 借助二叉树的非递归遍历

https://leetcode.com/problems/serialize-and-deserialize-bst/discuss/93224/Concise-iterative-Python-solution-using-stack-beat-99.

```Python
"""
    改进版1：队列+边界值
"""
class Codec:

    def serialize(self, root):
        vals = []

        def preOrder(node):
            if node:
                vals.append(node.val)
                preOrder(node.left)
                preOrder(node.right)

        preOrder(root)

        return ' '.join(map(str, vals))

    # O(N) since each val run build once
    def deserialize(self, data):
        vals = collections.deque(int(val) for val in data.split())

        def build(minVal, maxVal):              # 这个思路太巧妙。。有点消化不过来。。
            if vals and minVal < vals[0] < maxVal:
                val = vals.popleft()
                node = TreeNode(val)
                node.left = build(minVal, val)
                node.right = build(val, maxVal)
                return node

        return build(float('-infinity'), float('infinity'))
```        

```Python
"""
    改进版2：利用二叉树的【前序非递归遍历】实现！
"""
class Codec:

    def serialize(self, root):
        res, stk = [], []
        while stk or root:
            if root:
                res.append(str(root.val))
                stk.append(root)
                root = root.left
            else:
                root = stk.pop()
                root = root.right
        return ' '.join(res)

    def deserialize(self, data):
        if not data:
            return None
        data = map(int, data.split(' '))
        stk = []
        root = node = TreeNode(data[0])
        for n in data[1:]:
            if n < node.val:
                node.left = TreeNode(n)
                stk.append(node)
                node = node.left
            else:
                while stk and stk[-1].val < n:   # !!!
                    node = stk.pop()
                node.right = TreeNode(n)
                node = node.right
        return root
```
