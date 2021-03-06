### 902.Numbers-At-Most-N-Given-Digit-Set

可以用常规的DFS的方法将所有小于N的数都访问（构造）一遍，但是当N很大的时候就很低效。

比较高效的解法是多利用数学计算。假设数字N的位数是K，那么意味着任何小于K位的整数都不可能大于N，我们可以直接累加小于K位的整数的个数。

然后我们只需考虑K位的整数里有多少小于N的。我们可以从最高位开始递归构造。当选择的第i位上的数字小于num[i]的时候，剩余的K-i位数字可以任取，总数肯定不会超过N，因此可以直接加上```count+=pow(D.size(), K-i)```。当选择的第i位上的数字确定为num[i]的时候，我们就递归处理下一位即可。

注意在边界条件，当处理完所有的K位之后，这意味一路都是贴着上限走过来的，必须还要```count+=1```。这个代表着恰好所构造出来的上限N。
