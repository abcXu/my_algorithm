## 贪心算法理论

局部最优------->全局最优

贪心算法一般分为如下四步：

- 将问题分解为若干个子问题
- 找出适合的贪心策略
- 求解每一个子问题的最优解
- 将局部最优解堆叠成全局最优解

这个四步其实过于理论化了，我们平时在做贪心类的题目 很难去按照这四步去思考，真是有点“鸡肋”。

做题的时候，只要想清楚 局部最优 是什么，如果推导出全局最优，其实就够了。

## 题解

#### 1、分发饼干

[题目链接](https://leetcode.cn/problems/assign-cookies/)

```cpp
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        if(s.size()==0) return 0;	// 没有饼干
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        int index = s.size()-1;
        int result = 0;
        for(int i = g.size()-1;i>=0;i--){	// 遍历胃口
            if(index>=0&&s[index]>=g[i]){	// 先判断饼干数组已经遍历完，再判断饼干是否比孩子胃口大
                result++;
                index--;
            }
        }
        return result;
    }
};
```

时间复杂度 $O(nlog(n))$

#### 2、摆动序列

[题目链接](https://leetcode.cn/problems/wiggle-subsequence/

```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        if (nums.size() <= 1) return nums.size();
        int curDiff = 0; // 当前一对差值
        int preDiff = 0; // 前一对差值
        int result = 1;  // 记录峰值个数，序列默认序列最右边有一个峰值
        for (int i = 0; i < nums.size() - 1; i++) {
            curDiff = nums[i + 1] - nums[i];
            // 出现峰值
            if ((preDiff <= 0 && curDiff > 0) || (preDiff >= 0 && curDiff < 0)) {
                result++;
                preDiff = curDiff;
            }
            
        }
        return result;
    }
};
```













