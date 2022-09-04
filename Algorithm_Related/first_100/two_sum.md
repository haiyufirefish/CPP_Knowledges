```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int>dict;
        
        int n = nums.size();
        for(int i = 0;i<n;++i){
            if(dict.find(nums[i])!=dict.end()){
                return {dict[nums[i]],i};
            }else{
                 dict[target - nums[i]] = i;
            }
           
        }
        return {};
    }
};
```

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.
