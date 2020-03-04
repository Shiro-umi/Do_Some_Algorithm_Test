# 腐烂的橘子
>在给定的网格中，每个单元格可以有以下三个值之一：
>
>- 值 0 代表空单元格；
>- 值 1 代表新鲜橘子；
>- 值 2 代表腐烂的橘子。
>每分钟，任何与腐烂的橘子（在 4 个正方向上）相邻的新鲜橘子都会腐烂。
>
>返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1。
>
>示例1:
>输入：[[2,1,1],[1,1,0],[0,1,1]]
>输出：4
>
>示例 2：
>输入：[[2,1,1],[0,1,1],[1,0,1]]
>输出：-1
>解释：左下角的橘子（第 2 行， 第 0 列）永远不会腐烂，因为腐烂只会发生在 4 个正向上。
>
>示例 3：
>输入：[[0,2]]
>输出：0
>解释：因为 0 分钟时已经没有新鲜橘子了，所以答案就是 0 。

由于每个`烂橘子`只会影响到相邻的好橘子，所以我们应该找到`每一分钟内`与之`相邻`的格子。  
这就很容易联想到图的`广度优先遍历`，在这个题中我们只需要关注每个`烂橘子`在当前情况下有几个相邻的`好橘子`。  

## 广度优先遍历
再来复习一下标准的广度优先遍历，我们可以用**颜色标记法**来记录当前需要遍历的节点与已经遍历过的节点。例如：
```python
A - B
|
C
```
我们要从节点A开始做广度优先遍历，可以用两个队列来表示不同的颜色标记：
```python
gray = [A]   # 当前节点
black = []   # 已遍历节点
```
从A遍历相邻节点得到B和C，之后将A标记为黑色，并将B和C标记为灰色：
```python
count = len(gray)   # 记录当前层级共有多少灰色节点
# 遍历与A相邻的节点标记为灰色
# gray = [A,B,C]
for i in range(count):
    node = gray.pop(0)
    # do something
    # ...
    black.append(A)

# gray = [B,C]
# black = [A]
```
如果后续还有层级的话，我们只需要多加一个判断条件，判断一下将要加入`gray`中的节点是否已在`black`中出现，若存在的话就不需要再标记了。



## 一般实现
```python
class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:

        # 定义搜索函数
        def search():
            for grays in range(len(gray)):  # 在现有灰色节点中遍历
                i, j = gray.pop(0)  # 左端出队列
                for di, dj in direction:    # 按4个方向分别遍历
                    if (i+di, j+dj) in fresh and (i+di, j+dj) not in black: # 找到没坏(没有被标记为黑色)的好橘子
                        gray.append((i+di, j+dj))   # 标记为灰色(快坏了)
                        fresh.remove((i+di, j+dj))  # 从好橘子中移除
                black.append((i,j)) # 将当前节点标记为坏橘子

        x,  y, time = len(grid), len(grid[0]), 0
        direction = [(0,1),(0,-1),(-1,0),(1,0)] # 代表4个方向
        
        # 初始化队列，将一开始就是2的节点标记为灰色，1标记为fresh，black初始化为空
        gray, black, fresh = [], [], []
        for i in range(x):
            for j in range(y):
                if grid[i][j] == 2: gray.append((i, j))
                if grid[i][j] == 1: fresh.append((i, j))

        if not fresh:
            return 0    # 如果一开始没有好橘子直接返回0

        while gray:
            search()    # 如果存在灰色节点就不停遍历
            time += 1   # 每次遍历time+1

        if not fresh:
            return time-1   # 如果没有好橘子了就返回次数，要除去第一次，所以-1
        else:
            return -1   # 没有坏橘子了但是还有好橘子，输出-1
```

## 集合实现
这个实现方式非常巧妙，整体思路还是差不多，但是很巧妙的使用了集合运算的方式，直接从好橘子中将坏橘子排除除去。

```python
class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        x,  y, level = len(grid), len(grid[0]), 0
        direction = [(0,1),(0,-1),(-1,0),(1,0)]
        fresh = {(i,j) for i in range(x) for j in range(y) if grid[i][j] == 1}
        rot = {(i,j) for i in range(x) for j in range(y) if grid[i][j] == 2}

        while fresh:
            if not rot : return -1
            rot = {(i+di,j+dj) for i, j in rot for di, dj in direction if (i+di,j+dj) in fresh}     # 将好橘子中与坏橘子相邻的加入坏橘子集合
            fresh -= rot    # 使用集合减运算从好橘子集合中除掉坏了的
            level += 1

        return level
```

活用集合运算，思路值得学习。
