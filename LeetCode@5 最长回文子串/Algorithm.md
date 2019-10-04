># 给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。 
>  
>示例 1：  
>输入: "babad"  
>输出: "bab"  
>注意: "aba" 也是一个有效答案。  
>  
>示例 2：  
>输入: "cbbd"  
>输出: "bb"  

看似很简单的一道题，其实..  
为什么说它很简单呢，因为这道题只有一个关键的点，想通了问题就可以比较高效地解决。  
我们来想一下回文的定义：  
**将一个串倒序之后如果与原来的串想通则为回文**  
或者：  
**将一个串以中间位置对折若完全重合则为回文**  
下面看看如下例子：  
```
"abcba"
"abccba"  
```  
通过上面的例子结合上述定义不难发现，符合回文定义的字符串都存在一个性质：  
**回文串的两边各自-1得到的结果也必定是回文**  
也可以理解为：  
**回文串的两边各自+1相同的字符得到的结果也必定是回文**  
看到这里想必思路就明朗了很多。若是暴力遍历的话用脚后跟想一想都知道里面存在着巨量的冗余。  
但是根据上面总结到的性质，当我们找到一个回文的时候，如果它的两边+1字符相同的话，那么当前串就不是最长的回文子串。
  
# 递归法
分析到这个程度，我的脑子里首先浮现出的思路是`递归`。试想一下，如果我们通过一个判别方法`searchPalindrome(L,R)`知道了`s[L:R]`为回文子串，那么我们就可以在这个方法中直接递归调用`searchPalindrome(L-1,R+1)`来判断当前递归主体的字串`s[L,R]`是不是最长回文字串，或者说是判断`s[L-1,R+1]`是不是比`s[L,R]`更长的回文字串。  
```python  
def searchPalindrome(L, R):
    if isPalindrome(s[L:R]):
            searchPalindrome(L-1, R+1)
    else:
        return
```
下面我们还剩下一个需要解决的问题：  
根据上面举例的回文串，不难发现`abcba`和`abccba`都是回文串，也就是说**递归入口**必须加入两种不通结构的回文判别逻辑才能够解决问题。  
那么如何来解决这个问题呢？再来回忆一下回文的第二个定义，是不是也可以解释为**回文串都是中心对称的**，那么从这个角度出发，使用一个函数去表示这个中心，问题就迎刃而解了。当回文串长度为奇数的时候，这个中心应该计算为正中间的那个数。但当长度为偶数的时候，这个中心为`L`和`R`。  
抽象表示如下：  
```python  

# 抽象表示字串中心的L和R
center = calculate_center(s[L:R])
```
既然这个中心可以是`L`和`R`，也可以是一个`center`，那么是不是可以用两个指针来表示这两种状态呢？  
设两个指针`L`和`R`都为`0`，我们交替令两个指针`+1`，那么我们就可以发现两个指针在`重合`与`相邻`两种状态中不停转换，而这两种状态恰好等价于上面提到的两种情况。  
到此我们的实现结构就比较清晰了：  
```python  

class Solution:
    def longestPalindrome(self, s: str) -> str:
        self.mL, self.mR = 0, 0         # 设置最长回文串的L和R
            
        def searchPalindrome(L, R):
            if L >= 0 and R <= len(s)-1:
                if s[L] == s[R]:
                    if R-L >= self.mR - self.mL:
                        self.mL, self.mR = L, R                 # 更新最长回文串
                    searchPalindrome(L-1, R+1)              # 递归扩张
            else:
                return
           
        L, R = 0, 0            # 设置当前主体回文串的L和R
        while L < len(s)-1:
            searchPalindrome(L, R)
            if L == R:                  #计算中心索引
                R += 1
            else:
                L += 1
            
        return s[self.mL:self.mR+1]
```  
  
# 二分滑动窗口
考虑这样一个事实：  
若我们的字符串为`s`，并且`len(s)==100`，同时整个`s`中只存在一个符合要求的字串索引为`49-99`。也就是说这个字符串前一半没有回文，后一半全是回文。  
虽然情况比较极端，但还是能清晰明了地说明问题：若按照一般方法，索引`49`之前的所有字串遍历都是冗余的。为了避免这样的问题我们就应该考虑其他法。  
上面的递归法默认从最短字串长度`1`开始遍历，而我们要找到最长的回文子串，这个过程中就会产生冗余。  
我们可以采用一个滑动窗口，比较极端的情况，整个字符串都是回文，那如果我们将滑动窗口初始化长度定位`100`，那么经过一次字符串翻转就可以判断出结果。  
但是上面说的毕竟是极端情况，为了让这个方法更有普适性，所以我们需要将滑动窗口与二分法相结合。  
分析到这里思路就已经比较清晰了：  
我们先以`s`的长度(实现的时候考虑奇偶)初始化我们的窗口，对`s`进行一次扫描，每次窗口开始索引`+=1`。如果没发现回文的话，我们将窗口长度再次减半，继续从头扫描整个`s`，直到找到第一个回文子串`s[L:R]`。  
然后问题就又回到了回文的性质，假设当前窗口的长度为`x`，那么实际上长度在`x`到`2x`之间的字串我们是没有扫描过的。也就是说到这一步为止我们仅仅找到了最长回文字串所在的`长度区间`。  
**所以我们还需要以扩张当前窗口的方式遍历所有当前长度匹配到的所有回文子串**  
上面这句话比较绕，为了理解我们来考虑如下串：  
```python 
# 滑动窗口区间长度为 3
s = "qw aba fwe dcbcd"
```
为了方便区分在`s`中添加了分隔，其中可以看到又两个回文字串：`aba`和`dcbcd`。当滑动窗口区间为`3`的时候，该窗口首先找到`aba`，扩张后发现不构成回文。  
但`s`中还存在一个长度为`3`的回文字串`cbc`。所以我们还需要对`cbc`进行扩张，最后得到`dcbcd`作为结果。扩张窗口的时候我们同样采用二分的方法，将窗口扩张为`win+win//2`。  
```python 
class Solution:   
    def checkPolindrome(self, s: str) -> bool:
        return True if s == s[::-1] else False
    
    def longestPalindrome(self, s: str) -> str:
        
        ans, l, r = '', 0, len(s)
        while l <= r:
            mid = (l+r)//2
            palindrome = False
            
            # 这里需要同时遍历当前窗口和当前窗口长度+1，才能保证所有情况都被遍历到
            # 作者使用L和R判断窗口大小的手法很巧妙，值得学习
            for i in range(len(s)-mid+1):       
                if (True if s[i:i+mid] == s[i:i+mid][::-1] else False):
                    palindrome = True
                    ans = s[i:i+mid]
                    break
            for i in range(len(s)-mid):
                if (True if s[i:i+mid+1] == s[i:i+mid+1][::-1] else False):
                    palindrome = True
                    ans = s[i:i+mid+1]
                    break   
                    
            l, r = (mid+1, r) if palindrome else (l, mid-1)
            
        return ans

#作者：ma-xing
#链接：https://leetcode-cn.com/problems/longest-palindromic-substring/solution/ti-gong-yi-chong-xin-de-si-lu-er-fen-fa-by-ma-xing/
#来源：力扣（LeetCode）
```

# 动态规划  
终于出现了！每次提到动态规划我相信很多人都是懵逼的。  
但仔细想想，其实动态规划的思想并不难，用最简单的解释来说：  
**通过一个DP矩阵记录下已经得到的状态值，然后通过一个状态转移方程，利用已经得到的状态推断出当前状态**  
回顾上提到的回文串的性质，我们以`s`中的每一个字符作为坐标，建立一个`size*size`的二位DP矩阵表示`s[L:R]`的状态。将状态初始化为`False`表示非回文。那么根据上面的性质不难推断出如果`DP[L][R] == True`，那么当`s[L-1] == s[R+1]`的时候就能直接得出`s[L+1:R+1]`的状态为`True`。另一个特殊情况 ，`R-L <= 2`的时候只要`s[l] == s[R]`那么`s[L:R]`就一定为回文。  
那么我们的状态转移方程就可以进行如下表示：  
```python 
if s[L] == s[R] and (R-L <= 2 or dp[L+1][R-1]):
    dp[L][R] = True
```
话是这么说，但动态规划真正的难点是把思路转换为代码  
而这道题的另一个比较难的地方就是，**如何运用状态转移方程才能让算法变得高效**。 
思考下面的例子：  
```python  
s = 'babad'
```
观察上面的状态转移方程，我们选用了`dp[L+1][R-1]`作为判断条件是为了试图遍历最少的组合。当`s[l] == s[R]`的时候，只要`dp[L+1][R-1]`为`True`就可以直接得出 `DP[L][R]`为`True`。  
所以我们只需要保证短序列在长序列之前被遍历就好了。  
参考下面实现：  
```python  
class Solution:   
    def longestPalindrome(self, s: str) -> str:

        size = len(s)
        if size <= 1:
            return s
        
        longest = 1
        res = s[0]
        dp = [[False for _ in range(size)] for _ in range(size)]
        
        for R in range(1, size):
            for L in range(R):
                if s[L] == s[R] and (R-L <= 2 or dp[L+1][R-1]):
                    dp[L][R] = True
                    (longest, res) = (R-L, s[L:R+1]) if R+1-L > longest else (longest, res)
        
        return res
```