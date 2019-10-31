# Leetcode 刷题记录
## 2019.10.28—2019.11.4
### [Leetcode 73 矩阵置零]("https://leetcode-cn.com/problems/set-matrix-zeroes/")
**题目描述**

给定一个 m x n 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用原地算法。

**样例输入输出**

Input 1：[[1,1,1],[1,0,1],[1,1,1]]

Output 1:[[1,0,1],[0,0,0],[1,0,1]]

Input 2:[[0,1,2,0],[3,4,5,2],[1,3,1,5]]

Output 2:[[0,0,0,0],[0,4,5,0],[0,3,1,0]]

**解题思路**

1、遍历初始矩阵，如果发现如果某个元素 matrix[i][j] 为 0，我们将第 i 行和第 j 列的所有非零元素设成很大的负虚拟值MODIFIED。

2、遍历整个矩阵，将所有等于MODIFIED的元素设为0。

**golang代码**

	func setZeroes(matrix [][]int)  {
    	MODIFIED := -100000
    	row := len(matrix)
    	column := len(matrix[0])
    	for r := 0; r < row; r++ {
        	for c := 0; c < column; c++ {
            	if matrix[r][c] == 0 {
                	for m := 0; m < column; m++ {
                    	if matrix[r][m] != 0 {
                        	matrix[r][m] = MODIFIED
                    	}else {
                        	matrix[r][m] = 0
                    	}
                	}
                	for k := 0; k < row; k++ {
                    	if matrix[k][c] != 0 {
                        	matrix[k][c] = MODIFIED
                    	}else {
                        	matrix[k][c] = 0
                    	}
                	}
            	}
        	}
    	}
    	for r := 0; r < row; r++ {
        	for c := 0; c < column; c++ {
            	if matrix[r][c] == MODIFIED {
                	matrix[r][c] = 0
            	}
        	}
    	}
	}

### [Leetcode 74 搜索二维矩阵]("https://leetcode-cn.com/problems/search-a-2d-matrix/")
**题目描述**

编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

* 每行中的整数从左到右按升序排列。
* 每行的第一个整数大于前一行的最后一个整数。

**样例输入输出**

Input 1：[[1,3,5,7],[10,11,16,20],[23,30,34,50]]&ensp;&ensp;3

Output 1:true

Input 2:[[1,3,5,7],[10,11,16,20],[23,30,34,50]]&ensp;&ensp;13

Output 2:false

**解题思路**

1、首先用二分法找到第一次最后一个数大于target的那一行

2、在该行寻找target，若找到则输出ture，否则输出false

**golang代码**

    func searchMatrix(matrix [][]int, target int) bool {
    	if len(matrix) == 0 || len(matrix[0]) == 0{
        	return false
    	}

    	columnID := firstGreaterThanTarget(matrix, target)
    	return halfSearch(matrix[columnID], target)

	}

	func firstGreaterThanTarget(matrix [][]int, target int) int{
    	row_len := len(matrix[0])
    	left, right := 0, len(matrix) - 1
    	mid := left + (right - left) >> 1
    	for left <= right {
        	mid = left + (right - left) >> 1
        	midNum := matrix[mid][row_len - 1]
        	if midNum >= target {
            	if mid == 0 || matrix[mid - 1][row_len - 1] < target {
                	return mid
            	}
            	right = mid -1
        	} else {
           		left = mid + 1 
        	}
    	}
    	return mid
	}

	func halfSearch(k []int, target int) bool {
    	left, right := 0, len(k) - 1
    	for left <= right {
        	mid := left + (right - left) >> 1
        	if k[mid] == target {
            	return true
        	} else if k[mid] < target {
            	left = mid + 1
        	} else {
            	right = mid - 1
        	}
    	}
    	return false
	}