># 编辑距离  
>给定两个单词 word1 和 word2，计算出将 word1 转换成 word2 所使用的最少操作数。   
>你可以对一个单词进行如下三种操作：  
>  1.插入一个字符  
>  2.删除一个字符  
>  3.替换一个字符  
>示例:  
>输入: word1 = "horse", word2 = "ros"  
>输出: 3  
>解释:   
>horse -> rorse (将 'h' 替换为 'r')  
>rorse -> rose (删除 'r')  
>rose -> ros (删除 'e')  

编辑距离这个东西很有用。~~但是平时遇不到所以我就不学了~~  
**并不能!!**  
俗话说得好，书到用时方恨少，就算平时遇不到，为了找工作也得学！  

# 1.编辑距离是什么
所谓编辑距离是指，将word1经过特定操作转换为word2所需要的步骤数。  
这玩意有什么用？  
这玩意用处可大了。编辑距离越小可以反映出两个单词的相似度越高，并且在某些特定的情况下会有奇效。  
试想如果需要实现一个拼写自动校对工具，那么编辑距离是不是就派上用场了呢？通过编辑距离可以猜测用户希望输入的单词等等。  
  
# 2.思路  
这道题被分为困难不是没有原因的，先来想一想如何下手。  
举个例子，将`rad`转换为`apple`：  
```python  
  rad => apple
```
**没法下手 = =！**  
这tm怎么做嘛！你让我把`rad`变成`app`都费劲，还变`apple`呢？  
等一下，我好像想到了什么。  
复杂的问题我搞不定，我可以把问题简化啊！试想如果我真的把`rad`变成了`app`，那么就可以很快的得出再加2次额外操作就可以变为`apple`的结论。  
那么是不是可以再将问题缩小一些？比如我先把`ra`变成`app`？  
桥豆麻袋，好像把`ra`变成`app`的**最小**方法不止一种！  
1.以`ra`为base转换成`ap`然后再添加一个`p`  
2.以`r`为base先转换为`ap`然后再把`a`转换为`p`
3.以`r`为base先转换为`app`然后再把`a`删除(这道题例子比较简单，存在方法3开销小于方法2的情况)
仔细想一想似乎我们要的结果就是由1，2，3一共三种**状态**转换过来的。  
..........  
**这不就是动态规划嘛！**  
豁然开朗。来左边跟我一起画个图，右边...  
![1](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/1.png)  
看了图就明白了！我们需要求`dp[i][j]`的话，只需要选取`min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])`就好了。  
但是第一行和第一列怎么办？这个时候可以在矩阵中引入`""`空字符串，因为空字符串到一个单词的编辑距离就等于单词的长度。  
![2](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/2.png)  
再看一看我们上面例子中的情况：  
![3](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/3.png) 
到此为止是不是一目了然了呢？  
给出完整的dp矩阵，最后我们要的结果就是右下角的数字：  
![4](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4072%20%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB/4.png) 

# 3.实现
思路想明白了之后实现没有太大难度，看图说话就好了，直接上代码：
```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        l1, l2 = len(word1), len(word2)
        
        dp = [[max(l1,l2) for j in range(l2+1)] for i in range(l1+1)]
        
        for i in range(l1):
            dp[i][0] = i
        for i in range(l2):
            dp[0][i] = i
            
        for i in range(1,l1+1):
            for j in range(1,l2+1):
                if word1[i-1] == word2[j-1]:
                    # 如果相同则不需要任何操作，所以等于两边各去掉一个字母的情况
                    dp[i][j] = dp[i-1][j-1]
                    continue
                dp[i][j] = min(
                    dp[i-1][j-1] + 1,
                    dp[i-1][j] + 1,
                    dp[i][j-1] + 1
                )
        
        return dp[l1][l2]
```