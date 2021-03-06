*题目链接*

https://leetcode-cn.com/problems/russian-doll-envelopes/


*题目介绍*
********************************
*俄罗斯套娃信封问题*

给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 (w, h) 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

说明:
不允许旋转信封。

示例:

输入: envelopes = [[5,4],[6,4],[6,7],[2,3]]

输出: 3 

解释: 最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。

********

```python
class Solution:
    def maxEnvelopes(self, nums: List[List[int]]) -> int:
        # exception
        if not nums:
            return 0
        n = len(nums)
        # 排序
        nums = sorted(nums, key=lambda x: (x[0], -x[1]))
        # 最长递增子序列框架
        dp = [1] * n
        for i in range(n):
            for j in range(i):
                if nums[i][1] > nums[j][1]:
                    dp[i] = max(dp[i], dp[j] + 1)
        return max(dp)
```

*思路*

首先读题发现，这个无非是将[最长递增子序列](sub.md)扩展到二维而已，比较的元素从一个变成一个列表罢了

任何时候，我都鼓励读者从最简单的实例入手，比如 [[1,1],[2,2],[3,3]],这个很简单无非是遍历两个值比大小嘛
```python
n = len(nums)
for i in range(n):
    for j in range(i):
        if nums[i][0] > nums[j][0] and nums[i][1] > nums[j][1]:
...
```
接下来，不断上升。试试[[2,2],[1,1],[3,3]],之前的算法是不是是失效了，要思考原因，这里的原因是没有排序，导致[2,2]对应的*dp*值为*1* ，那么我们就先排序再复制上面的不就行了

```python
nums.sort()
#复制上面的即可
...
```
需要注意的是，*sort()* 不仅排一维列表,二维也可以，并且全都升序，但是上升到**三维**就失效了

有人说，这就结束了，不存在的，这可是道*hard*.如果你直接把这个代码提交，你会得到一个非常变态的测试数据，没错，**超时**！！原因是我们的*if 判断存在冗余*，那么，如何优化呢？

我们一开始是两个元素都比，有没有方法只比一个呢？

```python
nums = sorted(nums,key = lambda x: (x[0],-x[1]))
#按照第一个元素升序，第二个元素降序排列
if nums[i][1] > nums[j][1]:
```

除了*if*语句改了，其他的不变

那么，为什么要这么排呢？

举个例子，[[2,3],[5,4],[6,7],[6,4]]（这是排好的）,分为两种情况

*1.* 第一个元素不相等
    
    因为第一个都按照升序排列，并且不等，所以后面那个大于前面的，就一定满足

*2.* 第一个元素相等

    因为第二个按照降序排列，假设两个相等的为a,b.那么，对a来讲，a因为在前面，所以不会和b比较；对b来讲，虽然与a比较，但因为是降序，b的第二个一定比a来的小，综上，相等的没影响。

当然，这道题的最优解是*二分搜索*，并非*动态规划*，这里只是为了开阔读者视野

*拓展*

********************************
读者可能会觉得这里的排序略显骚气，其实*python*类似的语法不少，简洁好用。

```python
sorted(a,b)
```
*sorted*排序时有两个参数，一个是要排序的对象，另一个是依照什么排序，这个时候匿名函数就可以大显身手
类似的还有*filter*

```python
print(list(filter(lambda x : x % 2 == 0,range(1,10))))
```
这个会将*1-9*的偶数输出为一个列表，不同的是，两者对象和参照物的顺序不一样

*方法论*

做题的时候不是做题，要**建立在已经做过的题的基础上**，学会先搭框架，后完善。

思考的时候要满足*rules of recursition*，并且，**思维再怎么清晰，考虑的再怎么仔细都不为过**

