### Sliding window is a typical double pointer problem
The right pointer should first reach the target metrics.
Normally it uses two map to store and compare the current window
result and target. 

```
    string minWindow(string s, string t) {
        unordered_map<char,int>window;
        unordered_map<char,int>needs;
        for(char c:t)needs[c]++;
        int left = 0,right = 0;
        int min_len = INT_MAX,start = 0;
        int match = 0;
        
        while(right<s.size()){
            char r = s[right];
            if(needs.count(r)){
                window[r]++;
                if(window[r] == needs[r]){
                    match++;
                }
            }
            right++;
            
            while(match == needs.size()){
                    if(right-left<min_len){
                        min_len = right-left;
                        start = left;
                    }
                    char l = s[left];
                    if(window.count(l)){
                        window[l]--;
                        if(window[l]<needs[l]){
                            match--;
                        }
                    }
                    left++;
                
            }
        } 
            return min_len==INT_MAX?"":s.substr(start,min_len);
    }
```	