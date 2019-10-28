Leetcode 中等难度 篇
Author: lcy
Date: 2019年10月29日

[103. 二叉树的锯齿形层次遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/submissions/)

题目描述：给定一个二叉树，返回其节点值的锯齿形层次遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

解题思路；基于层序遍历，外加每层遍历时，设置一个变量，实现每两次翻转一次数据。 重点还是层序遍历。

基于python 实现的代码如下：
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        # 判空处理
        if not root:
            return
        
        vals = []
        nodes = [root]
        flag = False
        while len(nodes):
            lev = []
            size = len(nodes)
            for i in range(size):
                node = nodes.pop(0)
                lev.append(node.val)
                if node.left:
                    nodes.append(node.left)
                if node.right:
                    nodes.append(node.right)
            # 每两次转置一次数据
            if flag:
                lev.reverse()
            vals.append(lev)
            flag = not flag
        return vals
```
**时间复杂度为 O(n), 空间复杂度为 O(n)**

自己只实现了python 版本， 欢迎各位修改不足，或者给出其他版本的实现。

