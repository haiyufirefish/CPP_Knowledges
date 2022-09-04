Given an integer array nums, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

Notice that the solution set must not contain duplicate triplets.

 

**Example 1:**

```
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation: 
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.
The distinct triplets are [-1,0,1] and [-1,-1,2].
Notice that the order of the output and the order of the triplets does not matter.
```

```c++
    vector<vector<int>> nthSum(vector<int> &nums,int start,int target,int n){
        vector<vector<int>>ans;
        
        // two sum
        int sz = nums.size();
        if(n < 2 || sz < n)
            return ans;
        if(n == 2){
            int i = start,j = sz-1;
            while(i < j){
                int lvalue = nums[i],rvalue = nums[j];
                if(lvalue + rvalue < target){
                    while(i<j && nums[i] == lvalue)i++;
                }else if(lvalue + rvalue > target){
                    while(j>i && nums[j] == rvalue)j--;
                }else{
                    ans.push_back({lvalue,rvalue});
                    while(j>i && nums[j] == rvalue)j--;
                    while(i<j && nums[i] == lvalue)i++;
                }
            }
        }else{
            for(int i = start;i<sz;i++){
                auto vec = nthSum(nums,i+1,target-nums[i],n-1);
                for(auto &v:vec){
                    v.push_back(nums[i]);
                    ans.push_back(v);
                }
                while(i+1<sz && nums[i+1] == nums[i])i++;
            }
        }
        
        return ans;
    }
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(),nums.end());
        return nthSum(nums,0,0,3);
    }
```

nth sum special case: 3sum, two pointer with recursion.