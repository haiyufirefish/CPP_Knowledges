Given an integer array `nums` of length `n` and an integer `target`, find three integers in `nums` such that the sum is closest to `target`.

Return *the sum of the three integers*.

You may assume that each input would have exactly one solution.



**Example 1:**

```
Input: nums = [-1,2,1,-4], target = 1
Output: 2
Explanation: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

```c++
private:
  int twoSumClosest(vector<int>& nums, int start, int target) {
        int lo = start, hi = nums.size() - 1;
        // 记录两数之和与目标值的偏差
        int delta = INT_MAX;
        while (lo < hi) {
            int sum = nums[lo] + nums[hi];
            if (abs(delta) > abs(target - sum)) {
                delta = target - sum;
            }
            if (sum < target) {
                lo++;
            } else {
                hi--;
            }
        }
        return target - delta;
    }
public:
    int threeSumClosest(vector<int>& nums, int target) {
          if (nums.size() < 3) {
            return 0;
        }
        sort(nums.begin(),nums.end());
        // 记录三数之和与目标值的偏差
        int delta = INT_MAX;
        for (int i = 0; i < nums.size() - 2; i++) {
            // 固定 nums[i] 为三数之和中的第一个数，
            // 然后对 nums[i+1..] 搜索接近 target - nums[i] 的两数之和
            int sum = nums[i] + twoSumClosest(nums, i + 1, target - nums[i]);
            if (abs(delta) > abs(target - sum)) {
                delta = target - sum;
            }
        }
        return target - delta;
    }
```

The same as 3sum, but criteria is abs. 

