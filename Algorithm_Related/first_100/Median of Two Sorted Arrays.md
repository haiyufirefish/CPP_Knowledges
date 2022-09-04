Given two sorted arrays `nums1` and `nums2` of size `m` and `n` respectively, return **the median** of the two sorted arrays.

The overall run time complexity should be `O(log (m+n))`.



```c++
private:
    int kthelement(vector<int>& nums1, vector<int>& nums2,int k){
       vector<int>vec(k);
       int i = 0,j = 0 , h= 0;
        int n = nums1.size(), m = nums2.size();
        while(i < n && j <m &&h < k){
            if(nums1[i] < nums2[j]){
                vec[h++] = nums1[i++]; 
            }else{
                vec[h++] = nums2[j++];
            }
        }
        
        while(h < k){
            vec[h++] = i == n?nums2[j++]:nums1[i++];
        }
        return vec[k-1];
    }
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size();
        int n = nums2.size();
        
        if(!m)
            return n%2?nums2[n/2]:(nums2[(n-1)/2] + nums2[n/2])/2.0;
        
        if(!n)
            return m%2?nums1[m/2]:(nums1[(m-1)/2] + nums1[m/2])/2.0;
        
        if((m + n)%2)
            return kthelement(nums1,nums2,(m + n)/2+1);
        
        return (kthelement(nums1,nums2,(m + n)/2 + 1)+kthelement(nums1,nums2,(m + n)/2))/2.0;
    }
```

