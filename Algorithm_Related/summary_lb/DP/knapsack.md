```C++
int[][] dp[N+1][W+1]
dp[0][..] = 0 // not item 
dp[..][0] = 0 // no space
    //dp[i][w] is defined as follows: For the first i items, the current knapsack capacity is w, and the maximum value that can be loaded in this case is dp[i][w].

for i in [1..N]:
    for w in [1..W]:
        dp[i][w] = max(
            put i into knapsack
            not put into knapsack
        )
return dp[N][W]
```

If you didn't put the i-th item into the knapsack, then obviously the maximum value dp[i][w] should be equal to dp \[i - 1][w], inheriting the previous result.

If you put the i-th item into the knapsack, then dp[i][w] should be equal to val [i-1] + dp\[i-1][w - wt[i-1]].

```C++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        if(!amount)
            return 1;
        int sz = coins.size();
        if(!sz)
            return 0;
        
        //dp[i] is when the total number is i, dp[i] is the biggest number of combinations
        vector<int>dp(amount+1,0);
        dp[0] = 1;
        
        for(int &num : coins){
            for(int i = 0;i<=amount;++i){
                if(i >= num)
                    dp[i] += dp[i-num];
            }
        }
        return dp[amount];
        
    }
};

```

