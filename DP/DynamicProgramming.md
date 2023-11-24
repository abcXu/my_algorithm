# 概念

动态规划，英文：Dynamic Programming，简称DP，如果某一问题有很多重叠子问题，使用动态规划是最有效的。

所以动态规划中每一个状态一定是由上一个状态推导出来的，**这一点就区分于贪心**，贪心没有状态推导，而是从局部直接选最优的



例如：有N件物品和一个最多能背重量为W 的背包。第i件物品的重量是weight[i]，得到的价值是value[i] 。**每件物品只能用一次**，求解将哪些物品装入背包里物品价值总和最大。

动态规划中dp[j]是由dp[j-weight[i]]推导出来的，然后取max(dp[j], dp[j - weight[i]] + value[i])。

但如果是贪心呢，每次拿物品选一个最大的或者最小的就完事了，和上一个状态没有关系。

所以贪心解决不了动态规划的问题。



# 动态规划的解题步骤

1. 确定dp数组（dp table）以及下标的含义
2. 确定递推公式
3. dp数组如何初始化
4. 确定遍历顺序
5. 举例推导dp数组

# 相关操作

#### 斐波拉契数列

[题目地址](https://leetcode.cn/problems/fibonacci-number/description/)

* dp数组，第i个数的斐波那契数值是dp[i]
* 初始状态 dp[0] = 0,dp[1]=1
* 转移方程 dp[i] = dp[i - 1] + dp[i - 2]
* 从前到后遍历的

```cpp
class Solution {
public:
    int fib(int n) {
        if(n<=1) return n;

        vector<int> dp(n+1);
        dp[0] = 0;
        dp[1] = 1;
        for(int i = 2;i<=n;i++){
            dp[i] = dp[i-1]+dp[i-2];
        }

        return dp[n];
    }
};
```

#### 爬楼梯

[题目地址](https://leetcode.cn/problems/climbing-stairs/)

* 状态转移方程同上

```cpp
class Solution {
public:
    int climbStairs(int n) {
        if(n<=1) return n;
        vector<int> dp(n+1);
        dp[1] =1;
        dp[2] = 2;
        for(int i = 3;i<=n;i++){
            dp[i] = dp[i-1]+dp[i-2];
        }

        return dp[n];
    }
};
```

#### 使用最小花费爬楼梯

[题目地址](https://leetcode.cn/problems/min-cost-climbing-stairs/)

* $dp[i] = min(dp[i-1]+cost[i-1],dp[i-2]+cost[i-2])$
* $dp[0]!=cost[0],dp[0]=dp[1]=0$

* 因为，你可以选择从下标为 0 或下标为 1 的台阶开始爬楼梯

```c++
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        vector<int> dp(n+1);
        dp[0] = 0;
        dp[1] = 0;
        for(int i = 2;i<=n;i++){
            dp[i] = min(dp[i-2]+cost[i-2],dp[i-1]+cost[i-1]);
        }

        return dp[n];
    }
};
```

#### 不同路径

[题目链接](https://leetcode.cn/problems/unique-paths/)

* 初始状态 $dp[i][0]=dp[0][j] = 1$
* 转移方程$dp[i][j] = dp[i-1][j]+dp[i][j-1]$

```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m,vector<int>(n,0));
        for(int i = 0;i<m;i++) dp[i][0] = 1;
        for(int j = 0;j<n;j++) dp[0][j] = 1;
        for(int i =1;i<m;i++){
            for(int j = 1;j<n;j++){
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        }

        return dp[m-1][n-1];
    }
};
```

#### 不同路径Ⅱ

[题目链接](https://leetcode.cn/problems/unique-paths-ii/)

* 有了障碍，(i, j)如果就是障碍的话应该就保持初始状态（初始状态为0）。

* 但如果(i, 0) 这条边有了障碍之后，障碍之后（包括障碍）都是走不到的位置了，所以障碍之后的$dp[i][0]$应该还是初始值0。

  如图：

  ![63.不同路径II](https://code-thinking-1253855093.file.myqcloud.com/pics/20210104114513928.png)

  * **注意代码里for循环的终止条件，一旦遇到$obstacleGrid[i][0] == 1$的情况就停止$dp[i][0]$的赋值1的操作，$dp[0][j]$同理**

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        if(obstacleGrid[0][0]==1||obstacleGrid[m-1][n-1]==1) return 0;

        vector<vector<int>> dp(m,vector<int>(n,0));
        for(int i = 0;i<m&&obstacleGrid[i][0]==0;i++)  dp[i][0] = 1;
        for(int j = 0;j<n&&obstacleGrid[0][j]==0;j++) dp[0][j] = 1;

        for(int i = 1;i<m;i++){
            for(int j = 1;j<n;j++){
                if(obstacleGrid[i][j]==1) continue;
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```

#### 背包问题

![416.分割等和子集1](https://code-thinking-1253855093.file.myqcloud.com/pics/20210117171307407.png)

##### 0-1背包问题

* 即$dp[i][j]$ 表示从下标为[0-i]的物品里任意取，放进容量为j的背包，价值总和最大是多少。
* 

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int MAXN = 1005;
int v[MAXN];    // 体积
int w[MAXN];    // 价值 
int dp[MAXN][MAXN];  // f[i][j], j体积下前i个物品的最大价值 
int main(){
    int n, m;   
    cin >> n >> m;
    for(int i = 1; i <= n; i++) 
        cin >> v[i] >> w[i];
	// 考虑某个物品被装进背包先要看是否背包装得下，此外还要看是否要装
    for(int i = 1; i <= n; i++) 
        for(int j = 1; j <= m; j++)
        {
            //  当前背包容量装不进第i个物品，则价值等于前i-1个物品
            if(j < v[i]) 
                dp[i][j] = dp[i - 1][j];
            // 能装，需进行决策是否选择第i个物品
            else    
                dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - v[i]] + w[i]);
        }           

    cout << dp[n][m] << endl;

    return 0;
}
```

#### 划分等和子集

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum =0;
        // 计算总和
        vector<int> dp(10001,0);
        for(int i = 0;i<nums.size();i++){
            sum+=nums[i];
        }
		// 总和是奇数，直接返货false
        if(sum%2==1) return false;
        int target = sum/2;
		// 转换为0-1背包问题
        for(int i =0; i<nums.size();i++){
            for(int j =target;j>=nums[i];j--){
                dp[j] = max(dp[j],dp[j-nums[i]]+nums[i]);
            }
        } 

        if(target==dp[target]) return true;

        return false;
    }
};
```

