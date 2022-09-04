Given an array `nums` of distinct integers, return *all the possible permutations*. You can return the answer in **any order**.

 

**Example 1:**

```c++
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

```c++
class Solution {
private:
    unordered_map<int,int>visited;
    void dfs(vector<int> &nums,vector<vector<int>> &ans,vector<int> curr){
        if(curr.size() == nums.size()){
            ans.push_back(curr);
        }
        
        for(int i = 0;i<nums.size();++i){
            // if(std::find(curr.begin(), curr.end(), nums[i]) != curr.end()) 
            //     continue;
            if(visited[nums[i]])
                continue;
            visited[nums[i]] = 1;
            curr.push_back(nums[i]);
            dfs(nums,ans,curr);
            curr.pop_back();
            visited[nums[i]] = 0;
        }
    }
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        for(int num:nums){
            visited[num] = 0;
        }
        dfs(nums,ans,{});
        
        return ans;
    }
};
```

dfs + memo