#leetcode 路径总和 问题集锦

[112. 路径总和](https://leetcode-cn.com/problems/path-sum/comments/) 
**题目描述**：给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。
- 返回值为boolean 类型

**题目思路**：
因为本题只返回布尔值，所以只要当前找到一种符合的情况直接返回。相较于返回所有的可能性，难度上有所降低。
1. 先进行根节点的判空处理
2. 判断根节点是否满足条件
3. 递归查看子节点是否满足

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        if not root:
            return False
        if root.val == sum and not root.left and not root.right:
            return True
        return self.hasPathSum(root.left, sum - root.val) or self.hasPathSum(root.right, sum - root.val)
```

[113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)
**题目描述**：给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。
- 返回值为列表类型

**题目思路**：本题要求返回所有的可能情乱，所以需要记录。回溯的思路。
1. 根节点判空
2. 回溯处理
    - 判空处理，加入当前的节点值
    - 当前节点是否符合条件，1. 符合，加入记录表中； 2. 不符合，子节点判断
    - 弹出当前的节点值

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def backtrace(self, res, tmplist, node, num):
        if not node:
            return 
        tmplist.append(node.val)
        if node.val == num and not node.left and not node.right:
            res.append(tmplist.copy())
        else:
            self.backtrace(res, tmplist, node.left, num - node.val)
            self.backtrace(res, tmplist, node.right, num - node.val)
        tmplist.pop()
        
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        if not root:
            return []
        res = []
        self.backtrace(res, [], root, sum)
        return res
```
[437. 路径总和 Ⅲ](https://leetcode-cn.com/problems/path-sum-iii/submissions/)
**题目描述**：给定一个二叉树，它的每个结点都存放着一个整数值。找出路径和等于给定数值的路径总数。
路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。
- 返回值为数值类型

**题目思路**：
- 设置函数nodePath(node, num)， 实现以node 为起始节点，满足条件的可能性有多少。
- 对于主函数pathSum(root, sum), 是实现对于 root的分支，满足条件的可能性有多少。

因而在主函数中，需要使用nodePath 对左右子树进行求解。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def nodePath(self, node, num) -> int:
        if not node:
            return 0
        result = 1 if node.val == num else 0
        result += self.nodePath(node.left, num - node.val)
        result += self.nodePath(node.right, num - node.val)
        
        return result
        
    def pathSum(self, root: TreeNode, sum: int) -> int:
        if not root:
            return 0
        ans = 0
        ans += self.nodePath(root, sum)
        ans += self.pathSum(root.left, sum)
        ans += self.pathSum(root.right, sum)
        return ans
```
第二种思路，回溯法实现，但是利用了字典来优化。我们先解决这道题，思路有些相近，但基于数组上的数据实现，可能更易于理解。

[560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/submissions/)
**题目描述**：给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。
**题目思路**：使用字典存储 连续取值和的可能性。具体的思路可以参见 [文章](https://github.com/azl397985856/leetcode/blob/master/problems/560.subarray-sum-equals-k.md)

```python 
class Solution:
    def subarraySum(self, nums, k: int) -> int:
        # 存储从第0个元素到当前元素的和; 
        # 之所以要添加键值对 0:1, 是为了保证 mdict.get(val-k, 0) 时，返回1，意味着，从第0个元素到当前的位置的和恰好是k
        mdict = {0:1}
        val, ans = 0, 0
        for i in nums:
            val += i
            # val-k, 表示到当前下标多加出的数值V（为负值，直接返回0），若数值V 在字典中有出现，即表示可能从第0个位置到某一位置的和恰好是V，
            # 去掉这一部分，则恰好值为满足条件的k,而一共有字典[V]这么多情况
            ans += mdict.get(val-k, 0)
            mdict[val] = mdict.get(val, 0) + 1
        return ans
```
理解上述代码后，回到 437题。
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def backtrace(self, node, num, pathSum, mdict) -> int:
        if not node:
            return 0
        
        ans = 0
        pathSum += node.val
        ans += mdict.get(pathSum - num, 0)
        mdict[pathSum] = mdict.get(pathSum, 0) + 1
        
        ans += self.backtrace(node.left, num, pathSum, mdict)
        ans += self.backtrace(node.right, num, pathSum, mdict)
        
        mdict[pathSum] -= 1
        return ans
        
    def pathSum(self, root: TreeNode, sum: int) -> int:
        if not root:
            return 0
    
        return self.backtrace(root, sum, 0, {0:1})
```