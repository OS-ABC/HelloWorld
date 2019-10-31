[129. 求根到叶子节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/submissions/)

**题目描述**：给定一个二叉树，它的每个结点都存放一个 0-9 的数字，每条从根到叶子节点的路径都代表一个数字。计算从根到叶子节点生成的所有数字之和。
- 说明: 叶子节点是指没有子节点的节点。

解题思路一:
基于[113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)的解题思路去实现。
1. 采用递归的方式，将根节点到所有叶子节点的路径存储在列表res 中
2. 将res中的数值转换成符合题目要求的形式

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def backtrace(self, res, tmplist, node):
        if not node:
            return 
        tmplist.append(node.val)
        if not node.left and not node.right:
            res.append(tmplist.copy())
        self.backtrace(res, tmplist, node.left)
        self.backtrace(res, tmplist, node.right)
        tmplist.pop()
        
    def sumNumbers(self, root: TreeNode) -> int:
        if not root:
            return 0
        res = []
        self.backtrace(res, [], root)
        ans = 0
        # 计算数值
        for vals in res:
            tmp = ''
            for i in vals:
                tmp += str(i)
            ans += int(tmp)
            
        return ans
```
解题思路二：
参考于[文章](https://github.com/azl397985856/leetcode/blob/master/problems/129.sum-root-to-leaf-numbers.md)
递归思路：
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def helper(self, node, cur_val) -> int:
        if not node:
            return 0
        val = cur_val * 10 + node.val
        # 叶子节点，返回
        if not node.left and not node.right:
            return val
        
        left_val = self.helper(node.left, val)
        right_val = self.helper(node.right, val)
        
        return left_val + right_val
    
    def sumNumbers(self, root: TreeNode) -> int:
        return self.helper(root, 0)    
```

递推版：
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sumNumbers(self, root: TreeNode) -> int:
        if not root:
            return 0
        ans = 0
        a = eval(12)
        nodes, vals = [root], [root.val]
        while len(nodes):
            size = len(nodes)
            for i in range(size):
                node = nodes.pop(0)
                val = vals.pop(0)
                if node.left:
                    nodes.append(node.left)
                    vals.append(val * 10 + node.left.val)
                if node.right:
                    nodes.append(node.right)
                    vals.append(val * 10 + node.right.val)
                if not node.left and not node.right:
                    ans += val
        return ans
```
对于文章中提到的题目[1022. 从根到叶的二进制数之和](https://leetcode-cn.com/problems/sum-of-root-to-leaf-binary-numbers/submissions/)
只是将十进制转换为二进制
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def helper(self, node, cur_val):
        if not node:
            return 0
        # 二进制
        val = cur_val * 2 + node.val
        if not node.left and not node.right:
            return val
        left_val = self.helper(node.left, val)
        right_val = self.helper(node.right, val)

        return left_val + right_val

    def sumRootToLeaf(self, root: TreeNode) -> int:
        if not root:
            return 0
        return self.helper(root, 0)
```