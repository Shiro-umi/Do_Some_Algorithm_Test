># 最大子序和  
>给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。  
>示例:  
>输入: [-2,1,-3,4,-1,2,1,-5,4],  
>输出: 6  
>解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。  
  
本来没想把这道难度为简单的题单独拿出来总结的，但是当我真的去尝试解决这个问题的时候发现，我对这道题想考的东西一无所知。
  
# 1.复杂度O(n)的思路是怎么来的  
刚见这道题的时候完全没有头绪，满脑子都是暴力解法。无奈只能看看评论区有没有什么比较漂亮的思路。结果发现没有思路的原因是对**最大和子序的性质**不熟悉。  
经过对解法的阅读和理解发现，这个最大和子序列在非全负序列中**永远以非负数**开始。**(因为如果一个以负数开始的子序列去掉开头的数永远比原序列和要大)**  
其实根据LeetCode上的思路可以这么解释：  
负数对于序列和是负激励，而非负数对于序列和来说是单纯的激励。这个思想很关键，试想当序列和已经小于0的情况，证明目前的求和序列中从**最后一个非负数(如果存在)后面**负激励过大，那么从下一个数开始我们就可以把前面得到的大负激励序列全部丢弃。这个时候要明确一个问题，就是序列和小于0是因为**最后一个非负数(如果存在)后面负激励过大**，也就是说不存在从被丢弃的序列中间开始计和得到更大结果的情况，所以直接丢弃并不存在例外情况。我们只需要在遍历的过程中记录最大的累加和即可。  
参考如下思路图解，来源参考：[灵魂画师牧码](https://leetcode-cn.com/problems/maximum-subarray/solution/hua-jie-suan-fa-53-zui-da-zi-xu-he-by-guanpengchn/)   
![1](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4053%20%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C/1.png)
![2](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4053%20%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C/2.png)
![3](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4053%20%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C/3.png)
![4](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4053%20%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C/4.png)
![5](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4053%20%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C/5.png)
![6](https://raw.githubusercontent.com/Shiro-umi/Do_Some_Algorithm_Test/master/LeetCode%4053%20%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C/6.png)


# 2.代码实现
实现非常简单，想通了上面说的关键点，只需要另外用一个变量记录`res = max(res, sum)`最后返回就可以了：
```python  
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        res, n_sum = nums[0], 0
        
        for num in nums:
            n_sum = num if n_sum < 0 else n_sum+num
            res = max(n_sum, res)
            
        return res
```

