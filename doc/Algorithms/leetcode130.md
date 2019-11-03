###leetcoe 130
[130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/submissions/)

**题目描述**：给定一个二维的矩阵，包含 'X' 和 'O'（字母 O）。
找到所有被 'X' 围绕的区域，并将这些区域里所有的 'O' 用 'X' 填充。

**示例**:
```
X X X X
X O O X
X X O X
X O X X
```

运行你的函数后，矩阵变为：
```
X X X X
X X X X
X X X X
X O X X
```

**解释**:

被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。

**解题思路**：首先理解题目后，感觉要用深搜，在矩阵中存在两类元素，一类是要被改变的，即被包围的'O';一类是不需要改变的，即'X'和处于边缘的'O'。那么应该关注哪一部分呢？ 借鉴[文章](https://github.com/azl397985856/leetcode/blob/master/problems/130.surrounded-regions.md0),选择后者，扫描二维数组时，对于不会改变的'O'做好标记，之后再扫描一次，对没有标记的且要改变的数值进行改变。

```python
class Solution:
    # 标记与board[i][j]相邻的不需要改变的'O'
    def mark(self, board, rows, cols, i, j):
        if i < 0 or i >= rows or j < 0 or j >= cols or board[i][j] != 'O':
            return
        board[i][j] = 'A'
        self.mark(board, rows, cols, i + 1, j)
        self.mark(board, rows, cols, i - 1, j)
        self.mark(board, rows, cols, i, j + 1)
        self.mark(board, rows, cols, i, j - 1)

    def solve(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        rows = len(board)
        if rows < 2:
            return 
        cols = len(board[0])

        for i in range(rows):
            for j in range(cols):
                if 0 == i or rows - 1 == i or 0 == j or cols - 1 == j:
                    self.mark(board, rows, cols, i, j)
        
        for i in range(rows):
            for j in range(cols):
                if board[i][j] == 'O':
                    board[i][j] = 'X'
                elif board[i][j] == 'A':
                    board[i][j] = 'O'
```
