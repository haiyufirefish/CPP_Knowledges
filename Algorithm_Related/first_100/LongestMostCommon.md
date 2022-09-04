Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

 

**Example 1:**

```
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

```c++
 string longestCommonPrefix(vector<string>& strs) {
        // compare the last and first one
        int n = strs.size();
        
        if(n==1){
            return strs[0];
        }
        sort(strs.begin(),strs.end());
        string ans = "";
        string a = strs[0],b = strs[n-1];
        for(int i = 0;i<a.size();++i){
            if(a[i] == b[i]){
                ans+=a[i];
            }else{
                break;
            }
        }
        
        return ans;
    }
```

sort and compare

wrong answer: as flight, flower have the same size, but it would compare flower with flow, get the answer. But it can use minmax_element

```c++
  string longestCommonPrefix(vector<string>& strs) {
        // compare the last and first one
        int n = strs.size();
        
        if(n==1){
            return strs[0];
        }
        const auto [min, max] = minmax_element(strs.begin(),strs.end());
        string ans = "";
        
        string a = *min, b = *max;
        for(int k = 0;k<a.size();++k){
            if(a[k] == b[k]){
                ans+=a[k];
            }else{
                break;
            }
        }
        
        return ans;
    }
```

