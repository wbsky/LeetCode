### 题目：337. M. House Robber III

题目链接: https://leetcode.com/problems/house-robber-iii/

问题：二叉树版的小偷问题：从根节点开始偷盗，需沿着二叉树从上到下偷盗、不可连续偷盗两个相邻节点，求解最大的收益

#### 解题思路
* 首先，可以判断是个递归（对每个节点需进行相同的搜索），直接递归求解即可，但是会TLE
* 进一步，可以判断是DP（主问题由子问题构成，且求解中子问题会被计算多次），所以就可以利用递归+缓存的思路，降低时间复杂度 => 时间复杂度：O(N)，N为树中的节点个数
* 提升：参考高赞解法
    * 避免重复计算的核心是，找到**递归路径中子问题覆盖的原因，将每轮递归中这些重复计算的内容通过传参的形式保留下来**
    * 对每个节点的最优计算，依赖于rob本节点或不rob本节点两类情况的计算，故每轮计算返回两个结果：1) rob本节点时的最大收益；2)not rob本节点时的最大收益【注意，not rob本节点的话，可选择左右子树的任意最大值】


#### 代码
```Python
""" 法一：递归+缓存，beat 41.09% """
def rob(self, root: TreeNode) -> int:
        memo = {}
        
        def core(node):
            if node in memo.keys():
                return memo[node]
            if not node:
                return 0

            # dp1: rob the node; dp2: not rob the node
            dp1, dp2 = node.val, 0
            if node.left:
                dp1 += core(node.left.left) + core(node.left.right) 
                dp2 += core(node.left)

            if node.right:    
                dp1 += core(node.right.left) + core(node.right.right)
                dp2 += core(node.right)
            
            memo[node] = max(dp1, dp2)
            return memo[node]
        
        return core(root)

""" 法二：传递两个参数，beat 41.09% """        
def rob(self, root: TreeNode) -> int:
    
    def core(node):
        if not node:
            return 0, 0
        
        l = core(node.left)
        r = core(node.right)
        
        dp1 = l[1]+r[1]+node.val                  # rob the node
        dp2 = max(l[0], l[1]) + max(r[0], r[1])  # not rob the node !!!
        # not rob本节点，故该节点的最优值可以取左右子树中最大的
        return dp1, dp2
    
    return max(core(root))
```

### 题目：404. E. Sum of Left Leaves

题目链接: https://leetcode.com/problems/sum-of-left-leaves/

问题：二叉树左叶子节点求和

#### 解题思路
* 明确问题：明确左叶子节点的定义，首先，node为叶子节点；其次，node在其父亲节点的左分支上
* 基本思路：很显然需要递归；为了辅助判断node是否为其父亲节点的左分支，所以需要在传递节点的同时对此进行标记
* 设计实现：直接递归；或者用栈/队列进行搜索，模拟递归（更快些）

#### 代码
```Python
""" 法一：层序遍历，添加左子树标记，beat 84.13% """

def sumOfLeftLeaves(self, root: TreeNode) -> int:
    s = deque()
    s.append([root, False])
    ans = 0
    
    while s:
        node, isLeft = s.popleft()
        if not node:
            continue
        if (not node.left) and (not node.right):
            ans += (isLeft * node.val)
        if node.left:
            s.append([node.left, True])
        if node.right:
            s.append([node.right, False])
    
    return ans

  
""" 法二：改进版递归，未利用左子树标记，beat 56.29%"""

def sumOfLeftLeaves(self, root: TreeNode) -> int:
        
    if not root: return 0
    if root.left and (not root.left.left) and (not root.left.right):
        return root.left.val + self.sumOfLeftLeaves(root.right)
    return self.sumOfLeftLeaves(root.left) + self.sumOfLeftLeaves(root.right)
```

### 题目：437. E. Path Sum III

题目链接: https://leetcode.com/problems/path-sum-iii/

问题：二叉树从上到下搜索，找到满足和为sum的所有路径数

#### 解题思路
* 最简单的就是DFS了，对于每个节点，获取之前的路径累计值，再加入当前值，逐层传递，但是算法复杂度过高
* 参考高赞答案，借鉴2sum的解法，利用hash{key: the prefix sum, value: how many ways get to this prefix sum}，逐层求解
    * hash里面所存的都是从根节点到当前节点的路径总值
    * curSum-target == path 相当于是[从root->当前node路径值]-[从root->之前某node路径值]==target
    * 最初要存一个{0:1}，以实现找到从首个节点开始的满足要求的序列

#### 代码
```Python
""" 法二：借助hash的dfs，beat 95%"""       
def pathSum(self, root: TreeNode, sum: int) -> int:
        memo = {0:1}
        cnt = 0
        
        def core(node, preSum):
            if not node:
                return
            
            curSum = preSum + node.val
            if (curSum - sum) in memo.keys():
                nonlocal cnt                # 变量为值传递，直接调用外面的变量，需借助nonlocal
                cnt += memo[curSum - sum]
            
            if curSum in memo.keys():
                memo[curSum] += 1
            else:
                memo[curSum] = 1
            
            core(node.left, curSum)
            core(node.right, curSum)
            memo[curSum] -= 1           # dfs回溯时的取消操作！避免子节点计算值在回溯时产生影响
        
        core(root, 0)
        return cnt

""" 法一：简单的dfs，存在诸多冗余操作，beat 5%"""
def pathSum(self, root: TreeNode, sum: int) -> int:
        if not root:
            return 0
        
        res = []
        def core(node, vals):
            for i in range(len(vals)):
                vals[i] += node.val
                if vals[i] == sum:
                    res.append(1)
            vals.append(node.val)
            if vals[-1] == sum:
                res.append(1)
                
            c_vals = copy.deepcopy(vals)
            if node.left:
                core(node.left, vals)
            if node.right:
                core(node.right, c_vals)
        
        core(root, [])
        return len(res)
```
