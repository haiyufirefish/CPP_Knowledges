```c++
public:
    int lengthOfLongestSubstring(string s) {
        vector<int>dict(256,-1);
        int ans = 0;
        int j = -1;
        for(int i = 0; i < s.size();++i){
            if(dict[s[i]] > j){
               j = dict[s[i]];
            }
            dict[s[i]] = i;
            ans = max(ans,i-j);
        }
        
        return ans;
    }
```

Given a string `s`, find the length of the **longest substring** without repeating characters.