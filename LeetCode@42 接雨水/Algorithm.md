># 接雨水  
>给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。   
>示例：
>输入: [0,1,0,2,1,0,1,3,2,1,2,1]
>输出: 6
  
A：不就是接雨水吗？你就拿个桶，按最短的桶板取水..
B：等会..别光说啊..写起来
A：..

# 1.这道题到底想考什么？  
我们假设给定输入为： 
```python
[0,1,0,2,1,0,1,3,2,1,2,1]
```
这玩意画成图是这样的：  
![1](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/1.png)
其中红色阴影代表可以接下来的水量。  
按照常识，两边高中间低(存在坑)才可能存水，并且存水的高度以两边的最低为准。
那么可以用一个指针`p`指向每一个元素，当出现一个元素大于之前的起始元素的时候，计算两个元素之间的容量：
![1](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/1.png)  
但是有一个问题，上述方法仅仅在`p >= start`的情况下成立，例子中从`3`之后的元素就不成立了。  
所以我们可以反过来在用一个指针从后往前遍历，不就好了？  
所以这道题归根结底，是一个双指针的问题。  
  
# 2.思路  
有了前面的分析，我们知道可以通过两个指针分别去遍历图中存在的`坑`。  
但是有一个问题，考虑一种情况：当`p`指向`3`的时候如果继续遍历会浪费掉很多时间，所以应该让两个指针达到整组最高点的时候停下。  
然后又出现了新的问题，考虑下面的情况：  
```python
[2,0,3,0,3,0,2]
```
![1](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/1.png)
这一组中发现了两个最高点，连个指针都停下了，中间的怎么办？其实很容易想到，如果两边都是整组的最高点的话，那么两个最高点中间的任何元素都可以认为是`坑`，最坏的情况是中间是平的，那么`坑`的深度为0，统计进去也不会受到影响。  
那么问题到这就已经解决了：
```python  
class Solution:
    def trap(self, h: List[int]) -> int:
        p1, p2 = 0, len(h)-1
        top1, top2 = p1, p2
        water = 0 
        
        if h:
            maxh = max(h)
            while h[p1] < maxh:
                p1 += 1
                if h[p1] >= h[top1]:
                    top1 = p1
                else:
                    water += h[top1]-h[p1]

            while h[p2] < maxh:
                p2 -= 1
                if h[p2] >= h[top2]:
                    top2 = p2
                else:
                    water += h[top2]-h[p2]

            while p1 < p2:
                water += min(h[top1],h[top2])-h[p1]
                p1 += 1
            return water
        
        else:
            return 0
```
