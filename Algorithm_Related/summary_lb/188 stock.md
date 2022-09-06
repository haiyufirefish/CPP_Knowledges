You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day, and an integer `k`.

Find the maximum profit you can achieve. You may complete at most `k` transactions.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

```c++
class Solution {
public:
    // dp[i][k][0]:sell the stock at day i and k transcation is executed
    // one transaction means that buy and sell.
    
    int maxProfit(int k, vector<int>& prices) {
        int sz = prices.size();
        if(sz < 2){
            return 0;
        }
        
        vector<vector<vector<int>>>dp(sz,vector<vector<int>>(k+1,vector<int>(2,0)));
        for(int i = 0;i<sz;++i){
            for(int j = k-1;j >=0;--j){
                 if(i == 0){
                    dp[i][j][0] = 0;
                    dp[i][j][1] = -prices[i];
                    continue;
                }
                
                dp[i][j][0] = max(dp[i-1][j][0],dp[i-1][j][1] + prices[i]);
                dp[i][j][1] = max(dp[i-1][j][1],dp[i-1][j+1][0] - prices[i]);

                
            }
        }
        
        return dp[sz-1][0][0];
    }
};
```

