># 获得好友已观看视频
>有`n`个人，每个人都有一个`0`到`n-1`的唯一`id`。
> 
>给你数组`watchedVideos`和`friends`，其中`watchedVideos[i]`和`friends[i]`分别表示`id = i`的人观看过的视频列表和他的好友列表。

>`Level 1`的视频包含所有你好友观看过的视频，`level 2`的视频包含所有你好友的好友观看过的视频，以此类推。一般的，`Level`为`k`的视频包含所有从你出发，最短距离为`k`的好友观看过的视频。

>给定你的`id`和一个`level`值，请你找出所有指定`level`的视频，并将它们按观看频率升序返回。如果有频率相同的视频，请将它们按字母顺序从小到大排列。

>示例：

>           0 
>          / \ 
>         1   2 
>          \ / 
>           3 

>输入：watchedVideos = [["A","B"],["C"],["B","C"],["D"]], friends = [[1,2],[0,3],[0,3],[1,2]], id = 0, level = 1
>输出：["B","C"] 
> 
>解释：
>你的 id 为 0（绿色），你的朋友包括（黄色）：
>id 为 1 -> watchedVideos = ["C"] 
>id 为 2 -> watchedVideos = ["B","C"] 
>你朋友观看过视频的频率为：
>B -> 1 
>C -> 2

## 这题看起来很复杂，其实是个缝合怪

仔细看看这题的要求，一共有两点：

- 找到特定level的friends
- 统计friends看过的视频并排序

那我们就来一点一点的解决

## 找到特定level的friends

看一看上面示例结构，不难看出所谓统计特定level的friends其实就是图的广度优先搜索。

那么这一部分的考点其实就是**广度优先搜索**的实现。

广度优先搜索的思想主要是上色，按一种颜色标记节点是否被访问过，再按另一种颜色标记当前level的全部节点。

在上图中，例如`level1`，则可以记`0`为`黑色`，`1`和`2`为灰色，其中黑色代表已访问过，灰色代表当前level。

在实现上可以使用一种比较巧妙的办法：

- 用一个`双端队列`记录当前遍历到的所有节点，灰色节点从右边进，黑色节点从左边出

举个例子，当`level0`遍历完成的时候队列是这个样子的：
```python
[0]
```
这个节点目前是灰色的，我们根据它来遍历下一个level，得到如下状态：
```python
[0,1,2]
```
但其中节点`0`已经被使用过了，应该弹出，可是我们怎么知道上一级一共有多少个节点呢？
这需要在遍历下一个level之前做一个操作，将当前level的节点数保存一下：
```python
nodes = [0]
node_num = len(nodes)
nodes = find_next_level(); # node = [0,1,2]
for i in range(node_num):
    nodes.popleft()
```
另外需要注意的是，同样一个节点有可能多次遍历到，所以我们需要一个额外的数组来记录这个节点是否被使用过

这一部分实现如下：
```python
n = len(friends)    # friends数量
used = [False] * n      # 用于记录节点使用情况
q = collections.deque([id])     # 遍历主体，双向队列
used[id] = True     # 初始化，将本用户节点置True
for _ in range(level):      # 为了避免多余的运算，我们需要查询第几个level的friends就在这个level的range中遍历
    span = len(q)   # 记录队列长度
    for i in range(span):   
        u = q.popleft()     # 在这个长度中将所有节点标记为黑色
        for v in friends[u]:
            if not used[v]:     # 判断节点是否已被使用
                q.append(v)     # 将灰色节点加入双向队列
                used[v] = True      # 记录节点使用情况
```
这样返回的`q`就是我们要的level的所有节点

## 统计friends看过的视频并排序

接下来就很简单了，先统计，后排序，直接上实现：
```python
freq = collections.Counter()
for _ in range(len(q)):
    u = q.pop()
    for watched in watchedVideos[u]:
        freq[watched] += 1

videos = list(freq.items())
videos.sort(key=lambda x: (x[1], x[0]))

ans = [video[0] for video in videos]
return ans

```


## 实现
最后我们把这个缝合怪拼起来就完成了
```python
class Solution:
    def watchedVideosByFriends(self, watchedVideos: List[List[str]], friends: List[List[int]], id: int, level: int) -> List[str]:
        
        n = len(friends)
        used = [False] * n
        q = collections.deque([id])
        used[id] = True
        for _ in range(level):
            span = len(q)
            for i in range(span):
                u = q.popleft()
                for v in friends[u]:
                    if not used[v]:
                        q.append(v)
                        used[v] = True
        
        freq = collections.Counter()
        for _ in range(len(q)):
            u = q.pop()
            for watched in watchedVideos[u]:
                freq[watched] += 1

        videos = list(freq.items())
        videos.sort(key=lambda x: (x[1], x[0]))

        ans = [video[0] for video in videos]
        return ans
```


