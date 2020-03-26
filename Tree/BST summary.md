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
                cur = cur.right                             # 找到左子树的最右节
            root.val = cur.val                              # 进行替换
            root.left = self.deleteNode(root.left, cur.val)  # 递归对子树进行调整即可
        
    return root

```
