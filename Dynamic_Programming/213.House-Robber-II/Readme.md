### 213.House-Robber-II

#### 解法1：双状态动态规划
考虑环的影响，末位受到第一位的影响．所以我会分开来考虑第一位的选择．

如果选择打劫第一家，那么遍历到倒数第二家即可．因为倒数第一家不用考虑．现在第一家已经确定要打劫了，那么第二家的两个状态就也已经固定下来了： 不打劫的话，收益就是nums[0]；打劫的话是不允许的，收益是0（这里的理解需要有点技巧）.后续的每家，再继续按照dual status进行遍历．

如果选择不打劫第一家，那么可以遍历到倒数最后一家．现在第一家已经确定不打劫了，那么第二家的两个状态就也已经固定下来了： 不打劫的话，收益就是0；打劫的话是不允许的，收益是nums[1]．后续的每家，再继续按照dual status进行遍历．

至于状态转移方程：

此家打劫的收益　＝　上家不打劫的收益＋此家的价值

此家不打劫的收益　＝　max(上家不打劫的收益，上家打劫的收益）

#### 解法2：记忆化搜索（递归）
我们考虑是否打劫第零家（index为零）：是的话，那么下一步我们可以在[2,n-2]中任意选择打劫。如果不打劫第零家，那么下一步我们可以在[1,n-1]中任意选择打劫。也就是说，最顶层的答案就是```max( nums[0]+DFS(2,n-2), DFS(1,n-1) )```其中DFS的两个参数就是可供打劫的区间。这里特别注意的是n-2，这是因为题目中loop的要求（不能打劫任意相邻两家），打劫了第0家的话我们就不可能打劫第n-1家。

对于任意区间[i,j]作为打劫对象，我们可以同样处理，考虑是否打劫最前面的（也就是第i家）。根据不同的决策，我们分别有：```nums[i]+DFS(i+1,j)```和```DFS(i+1,j)```.两者分别递归处理得到结果后，取较大作为返回值。

当然，对于区间型的递归，我们需要记忆化来减少时间复杂度。

#### Follow-up
记忆化搜索虽然看上去不如第一种解法精妙，但是写法灵活，可以处理更多的问题。比如说有一个变种题：如果你打劫了第k家，那么第k-1和k+1家都不能被打劫，所有的人家同样是围城一个loop。问最多能打劫多少钱？

此题和LC213相同的地方是，无论如何都不可能取到两个相邻的元素。不同的地方是多了一个条件：无论如何都不能取超过n/3个元素。所以此题的本质是：取任意n/3个互不相邻的数，求最大的和。

当然，我们需要验证一下，是不是任意的n/3个互不相邻的元素集合，都可以按照题目中的取数规则来实现。事实上是可以的。例如```1000101010000```，有13个元素。其中1代表我们打算取的数。我们永远先取较为外层的数（随之删去左右相邻的两个零）：原序列可以得到```0010101000```，接下来```0101000```，接下来```1000```，最后```0```.

这样的题用双状态DP就没法做。但是用记忆化搜索就很方便。参考代码：
```cpp
class Solution {
    map<vector<int>,int>Map;
public:
    int rob(vector<int>& nums) 
    {
        if (nums.size()==0) return 0;
        int n = nums.size();
        return max(nums[0]+DFS(2,n-2,n/3-1,nums), DFS(1,n-1,n/3,nums));
    }
    
    int DFS(int i, int j, int k, vector<int>&nums)
    {
        if (k==0)
            return 0;
        
        if (j-i+1<k*2-1) return INT_MIN;
        
        if (Map.find({i,j,k})!=Map.end())
            return Map[{i,j,k}];
            
        if (k==1)
        {
            int mx = -1;
            for (int t=i; t<=j; t++)
                mx = max(mx, nums[t]);
            Map[{i,j,k}] = mx;
            return mx;            
        }            
        
        int ret = max(nums[i]+DFS(i+2,j,k-1,nums), DFS(i+1,j,k,nums));
        
        Map[{i,j,k}] = ret;
        
        return ret;
    }
};
```
