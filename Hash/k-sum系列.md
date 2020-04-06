### 题目：18. M. 4Sum

题目链接: https://leetcode.com/problems/4sum/

问题：k-sum问题系列，【先放个初版在这，接下来一周会着重研究k-sum系列问题】

#### 解题思路
* 借鉴2-sum, 3-sum的解法，利用hash算法，key:pair之和，value:[pair1, pair2]
* 问题的难点在于**去重**
    * 通过控制边界等方式可以实现去重，需要考虑得比较细致
    * 这里列出的解法主要是利用了python的set自动去重的功能，具有一定的取巧性


#### 代码
```Python
""" 哈希，beat 71.67% """
def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
    d = {}
    for i in range(len(nums)):
        for j in range(i+1, len(nums)):
            t_sum = nums[i]+nums[j]
            if t_sum in d.keys():
                d[t_sum].append([i, j])
            else:
                d[t_sum] = [[i, j]]
    
    res = set()
    for p1 in d:
        p2 = target-p1
        if p2 in d.keys():
            list1, list2 = d[p1], d[p2]
            for i,j in list1:
                for m, n in list2:
                    if i!=m and i!=n and j!=m and j!=n:  
                    # 去重1：确保不会在结果中利用同一个值多次
                        res.add(tuple(sorted([nums[i], nums[j], nums[m], nums[n]])))
                        # 去重2：排序后变为tuple加入set，保证最终的结果中无重复
                        
    return list(res)
```
