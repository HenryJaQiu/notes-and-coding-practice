#### [62. 不同路径](https://leetcode-cn.com/problems/unique-paths/)

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

求路径总数。

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        que = [1] * m
        for i in range(1,n):
            for j in range(1,m):
                que[j] = que[j] + que[j-1]
        return que[-1]
```

逐行遍历构建DP(m,n)，因为只与上一行相关可优化空间至Max(m,n)。

转移方程：DP(i,j) += min(DP(i,j-1), DP(i-1,j))

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        return comb(m+n-2,m-1)
```

数学方法，二元随机取值m+n-2次，其中m/n次值为限定条件，转化为C(m,m+n-2) = (m+n-2)! / m! 



#### [63. 不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        map = [0]*len(obstacleGrid[0])
        if obstacleGrid[0][0] == 0:
            map[0] = 1
        for i in range(len(obstacleGrid)):
            for j in range(len(obstacleGrid[0])):
                if obstacleGrid[i][j] == 0:
                    if i>0 and j>0:
                        map[j] = map[j] + map[j-1]
                    elif i>0:
                        map[j] = map[j]
                    elif j>0:
                        map[j] = map[j-1]
                else:
                    map[j] = 0
            #print(map)
        return map[-1]
```

同理，DP(m*n)，遇到障碍物则赋值为0，再做空间优化。

#### [64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        map = grid[:][:]
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if i>0 and j>0:
                    map[i][j] += min(map[i-1][j],map[i][j-1])
                elif i>0:
                    map[i][j] += map[i-1][j]
                elif j>0:
                    map[i][j] += map[i][j-1]
        return map[-1][-1]
```

同理DP(m*n)。

```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        map = grid[0][:]
        for i in range(0,len(grid)):
            for j in range(len(grid[0])):
                if i>0 and j>0:
                    map[j] = min(map[j],map[j-1]) + grid[i][j]
                elif i>0:
                    map[j] += grid[i][j]
                elif j>0:
                    map[j] += map[j-1]
            #print(map)
        return map[-1]
```

同理压缩空间。

#### [120. 三角形最小路径和](https://leetcode-cn.com/problems/triangle/)

给定一个三角形 triangle ，找出自顶向下的最小路径和。

每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。

```python
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        res = [0] * len(triangle)
        res[0] = triangle[0][0]
        for i in range(1,len(triangle)):
            for j in range(i,-1,-1):
                if 0<j<len(triangle[i])-1:
                    res[j] = min(res[j-1],res[j]) + triangle[i][j]
                elif j == 0:
                    res[j] = res[0] + triangle[i][j]
                elif j == len(triangle[i]) - 1:
                    res[j] = res[j-1] + triangle[i][j]
        return min(res)
```

同理DP(n^2)，压缩空间至n。为避免计算依赖问题，每行都从后往前算。

#### [931. 下降路径最小和](https://leetcode-cn.com/problems/minimum-falling-path-sum/)

给你一个 n x n 的 方形 整数数组 matrix ，请你找出并返回通过 matrix 的下降路径 的 最小和 。

下降路径 可以从第一行中的任何元素开始，并从每一行中选择一个元素。在下一行选择的元素和当前行所选元素最多相隔一列（即位于正下方或者沿对角线向左或者向右的第一个元素）。具体来说，位置 (row, col) 的下一个元素应当是 (row + 1, col - 1)、(row + 1, col) 或者 (row + 1, col + 1) 。

```python
class Solution:
    def minFallingPathSum(self, matrix: List[List[int]]) -> int:
        n = len(matrix)
        fo = matrix[0][:]
        cu = fo[:]
        for i in range(1,n):
            for j in range(n):
                if 0<j<n-1:
                    cu[j] = min(fo[j-1],fo[j],fo[j+1]) + matrix[i][j]
                elif j>0:
                    cu[j] = min(fo[j-1],fo[j]) + matrix[i][j]
                elif j<n-1:
                    cu[j] = min(fo[j],fo[j+1]) + matrix[i][j]
            fo = cu[:]
            #print(fo)
        return min(cu)
```

同理DP遍历，用2n优化空间。

#### [1289. 下降路径最小和  II](https://leetcode-cn.com/problems/minimum-falling-path-sum-ii/)

给你一个整数方阵 arr ，定义「非零偏移下降路径」为：从 arr 数组中的每一行选择一个数字，且按顺序选出来的数字中，相邻数字不在原数组的同一列。

请你返回非零偏移下降路径数字和的最小值。

```python
class Solution:
    def minFallingPathSum(self, arr: List[List[int]]) -> int:
        n = len(arr)
        fo = arr[0][:]
        cu = fo[:]
        for i in range(1,n):
            for j in range(n):
                if 0<j<n-1:
                    cu[j] = min(fo[j-1],fo[j+1]) + arr[i][j]
                elif j>0:
                    cu[j] = fo[j-1] + arr[i][j]
                elif j<n-1:
                    cu[j] = fo[j+1] + arr[i][j]
            fo = cu[:]
            #print(fo)
        return min(cu)
```

基于原思路修改后未通过所有用例，发现看错题目，可以从任意非同列而非相邻列下降。

```python
class Solution:
    def minFallingPathSum(self, arr: List[List[int]]) -> int:
        n = len(arr)
        fo = arr[0][:]
        cu = fo[:]
        for i in range(1,n):
            for j in range(n):
                if 0<j<n-1:
                    cu[j] = min(min(fo[:j]),min(fo[j+1:])) + arr[i][j]
                elif j>0:
                    cu[j] = min(fo[:j]) + arr[i][j]
                elif j<n-1:
                    cu[j] = min(fo[j+1:]) + arr[i][j]
            fo = cu[:]
            #print(fo)
        return min(cu)
```

此时时间复杂度为n^3，可以通过提前计算每列除该行外最小值降低一阶计算量。

```python
class Solution:
    def minFallingPathSum(self, arr: List[List[int]]) -> int:
        n = len(arr)
        fo = arr[0][:]
        cu = fo[:]
        for i in range(1,n):
            curMin = min(fo)
            curMinIndex = fo.index(curMin)
            mfo = [curMin] * n
            if 0<curMinIndex<n-1:
                mfo[curMinIndex] = min(min(fo[:curMinIndex]),min(fo[curMinIndex+1:]))
            elif curMinIndex>0:
                mfo[curMinIndex] = min(fo[:curMinIndex])
            elif curMinIndex<n-1:
                mfo[curMinIndex] = min(fo[curMinIndex+1:])

            for j in range(n):
                cu[j] = mfo[j] + arr[i][j]

            fo = cu[:]
            #print(fo)
        return min(cu)
```

这样快很多，虽然过程有点丑陋，但是因为要去吃饭了，优化下次再说。