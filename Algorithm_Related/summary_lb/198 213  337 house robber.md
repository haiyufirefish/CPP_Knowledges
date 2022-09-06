You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***.

```c++
class Solution {
public:
    // dp[i] = max(dp[i-2]+nums[i])
    int rob(vector<int>& nums) {
        int sz = nums.size();
        if(sz == 1)
            return nums[0];
        int dp0 = nums[0];
        int dp1 = max(nums[1],nums[0]);
        int dp2;
        for(int i = 2;i<sz;++i){
            dp2 = max(dp1,dp0 + nums[i]);
            dp0 = dp1;
            dp1 = dp2;
        }
        return dp2;
    }
};
```

```c++
class Solution {
private:
    int rob_range(vector<int> &nums,vector<int> dp,int start,int end){
        
        for(int i =start;i<end;++i){
            dp[2] = max(dp[1],dp[0]+nums[i]);
            dp[0] = dp[1];
            dp[1] = dp[2];
        }
        return dp[2];
    }
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 1)return nums[0];
        // 
        if(n == 2)return max(nums[0],nums[1]);
        vector<int>dp(3,0);    
        int res = max(rob_range(nums,dp,0,n-1),rob_range(nums,dp,1,n));
        return res;
    }
};
```

The thief has found himself a new place for his thievery again. There is only one entrance to this area, called `root`.

Besides the `root`, each house has one and only one parent house. After a tour, the smart thief realized that all houses in this place form a binary tree. It will automatically contact the police if **two directly-linked houses were broken into on the same night**.

Given the `root` of the binary tree, return *the maximum amount of money the thief can rob **without alerting the police***.

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    unordered_map<TreeNode*,int>memo;
public:
    int rob(TreeNode* root) {
        //bfs each 2 floor compare 
        if(!root)
            return 0;
        
        if(memo.find(root)!=memo.end())
            return memo[root];
        int rob_ = root->val + (root->left?(rob(root->left->left) +rob(root->left->right)):0)+(root->right?(rob(root->right->left) +rob(root->right->right)):0);
        int non_rob = rob(root->left) + rob(root->right);
        int res = max(rob_,non_rob);
        
        return memo[root] = res;
    }
};
```

