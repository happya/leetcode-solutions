---
title: dp
tags: dynamic programming
notebook: Leetcode
---

## 1025 divisor-game
---

```python
class Solution:
    def divisorGame(self, N: int) -> bool:
        if N==1:
            return False
        if N==2:
            return True
        dp=[False]*(N+1)
        dp[2] = True
        for i in range(2,N+1):
            for j in range(1,i//2+1):
                if i%j==0 and not dp[i-j]:
                    dp[i]=True
                    break
        return dp[-1]
```
思路

- 开辟一个数组`dp`用来存储从1到N时，Alice的理想结果。

- 对于每个dp[i],从1开始遍历到`i//2+1`，含义为第一次Alice报出的`x`,如果`i%x==0`，其对应的结果为`dp[N-x]`，只要有一个为`True`,即可得出对当前的`i`,ALice的最优结果，可存入`dp[i]`

- 反之，如果对这些可取的`x`,Alice都不能赢，那么存入`False`。



## 338 Counting bits

- 对于一个数`x`,假设其二进制表达形式为`111110100`,那么其中`1`出现的个数为`y=111110000`中出现1的个数加`1`,而`y`即为`x`中从低位往高位数，第一个`1`出现时，将其更高位不变，其余位变为`0`的结果。
- 怎么样得到`y`呢？如果其最后一位为`1`,直接`x-1`即可；如果最后一位为0，减去1后该位变为1，且前面的0均变为`1`,直到第一个`1`出现：这个`1`变为0，其其余更高位不变。
- 根据这个规律，`y=x&(x-1)`。


```python
class Solution:
    def countBits(self, num: int) -> List[int]:
        if num==0:
            return [0]
        if num==1:
            return [0,1]
        dp=[0]*(num+1)
        dp[1]=1
        for i in range(2,num+1):
            dp[i]=dp[i&(i-1)]+1
            
        return dp

```

# 121 best time to buy and sell stock

思路：
- 设定两个指示变量保存当前的最低价和能或得的最大利益，分别初始化为极大值和0；
- 遍历整个价格数组，遇到低价就保存，并更新当前的最低价；遇到高价就看卖出去的获利值，更新当前最大利益值

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        best_profit=0
        lowest_p=float('inf')
        for p in prices:
            if p<lowest_p:
                lowest_p=p
            elif p-lowest_p>best_profit:
                best_profit=p-lowest_p
        return best_profit

```


### 646. Maximum length of Pair Chain 
```python
class Solution:
    def findLongestChain(self, pairs: List[List[int]]) -> int:
        if not pairs:
            return 0
        if len(pairs)==1:
            return 1
        pairs.sort(key=lambda x:x[1])
        #print(pairs)
        n=len(pairs)
        sum=1
        end=pairs[0][1]
        for i in range(1,n):
            if(pairs[i][0]>end):
                sum+=1
                end=pairs[i][1]
        return sum
        
```

### 931 Minimum Falling Path

```python
class Solution:
    def minFallingPathSum(self, A: List[List[int]]) -> int:
        n = len(A)
        dp = [[0]*n for _ in range(n)]
        for i in range(n):
            dp[0][i]=A[0][i]
            
        for i in range(1,n):
            for j in range(n):
                dp[i][j]=dp[i-1][j]+A[i][j]
                if j==0:
                    dp[i][j]=min(dp[i][j],dp[i-1][j+1]+A[i][j])
                if j==n-1:
                    dp[i][j]=min(dp[i][j],dp[i-1][j-1]+A[i][j])
                if 0<j<n-1:
                    dp[i][j]=min(dp[i][j],A[i][j]+dp[i-1][j+1],A[i][j]+dp[i-1][j-1])
        return min(dp[n-1])
```



## 474: Ones and Zeros
```python
class Solution:
    def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
        dp={(0,0):0}
        for i,s in enumerate(strs):
            c0,c1=self.countZeroOne(s)
            newDp=collections.defaultdict(int,dp)
            for (l0,l1) in dp.keys():
                if 0<=c0+l0<=m and 0<=c1+l1<=n:
                    newDp[c0+l0,c1+l1]=max(newDp[c0+l0,c1+l1],dp[l0,l1]+1)
            dp=newDp
        return max(dp.values())
    
    def countZeroOne(self,s):
        c0=sum(c=='0' for c in s)
        return c0,len(s)-c0
        
```

### 413 Arithmetic Slices I

#### Solution 1

```python
class Solution:
    def numberOfArithmeticSlices(self, A: List[int]) -> int:
        n=len(A)
        if n<3:
            return 0
        dp=[0]*n
        sum =0
        for i in range(2,n):
            if(A[i]-A[i-1]==A[i-1]-A[i-2]):
                dp[i]=dp[i-1]+1
                sum += dp[i]
        return sum
```
#### Solution 2:
```python
class Solution:
    def numberOfArithmeticSlices(self, A: List[int]) -> int:
        n=len(A)
        if n<3:
            return 0
        dp=[{} for _ in range(n)]
        res=0
        for i in range(1,n):
            j=i-1
            diff = A[i]-A[j]
            if diff in dp[j]:
                dp[i][diff]=dp[j][diff]+1
                res+=dp[i][diff]
            else:
                dp[i][diff]=0
            #print('i= ',i,' ',dp[i],'res= ',res)
        return res
        
```


### 446 Arithmetic Slices II

```python
class Solution:
    def numberOfArithmeticSlices(self, A: List[int]) -> int:
        n=len(A)
        if n<3:
            return 0
        dp=[collections.defaultdict(int) for _ in range(n)]
        res=0
        for i in range(n):
            for j in range(i):
                diff = A[i]-A[j]
                dp[i][diff]+=1
                if diff in dp[j]:
                    dp[i][diff]+=dp[j][diff]
                    res+=dp[j][diff]
            #print('i= ',i,' ',dp[i],'res= ',res)
        return res
        
```

### 714 best time to 

### 712