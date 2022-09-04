```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        next_permutation(nums.begin(),nums.end());
    }
};
```

using strl or 

```c++
class Solution {
public:
    // find out the first nums[j], which nums[j + 1] > nums[j]
    // case 1: j < 0: means that next permuation is just reverse of array
    // case 2: j > 0: find out the n-1 .. 0 where nums[i] > num[j] swap[i,j]
    //reverse begin() + j + 1,end()
    void nextPermutation(vector<int>& nums) {
        int j = nums.size() -2;
        for(;j>=0;--j){
            if(nums[j+1] > nums[j]){
                break;
            }
        }
        
        if(j < 0){
            reverse(nums.begin(),nums.end());
            return;
        }else{
            int i = nums.size()-1;
            for(; i>j;--i){
                if(nums[i] > nums[j])
                    break;
            }
            
            swap(nums[i],nums[j]);
            reverse(nums.begin()+j+1,nums.end());
            return;
        }
    }
};
```

