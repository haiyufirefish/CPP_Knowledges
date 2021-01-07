Leetcode problem 300
dp[j] = max(dp[i]+1,dp[j])
```
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int>dp(n,1);
        for(int i = 0;i<n;i++){
            for(int j = i+1;j<n;j++){
                if(nums[j]>nums[i]){
                    dp[j] = max(dp[i]+1,dp[j]);
                }
            }
        }
        return *max_element(dp.begin(),dp.end());
    }
```
Optimization
For a subsequence only the last number matters.
dp[i] the smallest ending number of a subsequence that hast length i+1.
Init:dp[]
For each num, we can use it to:
1.Extend the longest subsequence
2.Replace a number to generate a better subsequence

dp is increasing, we can use binary seach to find the position to insert it.
Time complexity: O(nlogn)
Space complexity: O(n)
```
vector<int>dp;
        for(int n : nums){
            auto it = lower_bound(begin(dp),end(dp),n);
            if(it == end(dp)){
                dp.push_back(n);
            }else{
               *it = n; 
            }
                
        }
        return dp.size();
```
