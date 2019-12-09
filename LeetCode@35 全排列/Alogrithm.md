># 全排列  
>给定一个没有重复数字的序列，返回其所有可能的全排列。   
>示例：  
>输入: [1,2,3]  
>输出:  
>[ [1,2,3] , [1,3,2] , [2,1,3] , [2,3,1] , [3,1,2] , [3,2,1] ]  
  
找全排列，最简单的办法就是数呗。  
  
# 这个题想考什么？  
解题方法很简单，就是一个一个数，把每个情况都数出来，结果就有了。  
至于方法我觉得但凡智力正常的同学都能知道：  
`按顺序固定每一位的元素`，最后得到的所有组合的集合就是全排列。比如： 
`[2,1,3] -> [2,3,1] -> [3,1,2] -> [3,2,1]`  
在上面的例子中我们分别在第一位固定了`2`和`3`两个元素，但是我们会发现在遍历的过程中我们的操作元素从第一位转移到了最后一位，所以对于代码实现来说，需要一个**回溯**的过程。   
那么这个题的考点应该就是**回溯的实现**。
  
# 实现  
实现起来也不难，为了更好地理解回溯的过程先上一张从leetcode上弄下来的图。  
![1](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4042%20%E6%8E%A5%E9%9B%A8%E6%B0%B4/1.png)  
从图中我们可以更直观地看出在我们固定了每一个位置的元素之后发生了什么。  
这个过程中一共发生了两种操作：  
  
**一 是`按顺序固定每一位的元素`，看图很容易想明白，其实就是`递归`。**  
**二 就是回溯。每进行完一个分支我们需要回到根结点再进行其他分支。**  
  
那么思路就很清晰了，先看递归部分完成一个分支的遍历：  
```python  
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:

        res = []
        n = len(nums)
        
        def search(s, n):
            if s == n:
                res.append(nums[:])
            for i in range(s, n):
                nums[s], nums[i] = nums[i], nums[s]
                search(s+1, n)
        
        search(0, n)
        return(res)
```
上面这段代码是完全按照上面的图实现的，可以看出每次递归的时候起始位置`s`都会后移一位，因为上一位已经被固定了。而遍历到最后的时候会出现`s = n`的必然结果，所以根据这个条件可以判断当前分支是否遍历完成。若遍历完成，则保存当前排列。 
那么如何进行回溯呢？很简单，我们只需要在每一次递归之后将先前交换的两个元素复位，就完成了回溯。  
```python  
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:

        res = []
        n = len(nums)
        
        def search(s, n):
            if s == n:  # 判断是否为最终排列的条件
                res.append(nums[:])
            for i in range(s, n):
                nums[s], nums[i] = nums[i], nums[s]  # 交换
                search(s+1, n)
                nums[s], nums[i] = nums[i], nums[s]  # 回溯

        search(0, n)
        return(res)
```
