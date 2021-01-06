 The leetcode problem 55.
 it can have two solutions for it.
 One is greedy and another is dp.
 Greedy:
 ```
  bool canJump(vector<int>& nums) {
        int n = nums.size();
        int i = 0;
        for(int reach = 0;i<n&&i<=reach;i++){
            reach = max(reach,nums[i]+i);
        }
        return i==n;
    }
//If reach can reach somewhere, it can also reach where smaller than that
// So just increase i until it reaches n or reach
```
Dp:Looking from the end and at each point ahead checking the best possible way to reach the end.
```
 bool canJump(vector<int>& nums) {
        if(nums.size()<=1) return true;
        if(nums[0] ==0)return false; 
        vector<bool> dp(nums.size(),false); 
        dp[0] = true;
        int n = nums.size();
        for(int i=1;i<n;i++){
            for(int j = i-1;j>=0;j--){
                if(dp[j] && ((nums[j]+j)>=i)){
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n-1];
    }
```