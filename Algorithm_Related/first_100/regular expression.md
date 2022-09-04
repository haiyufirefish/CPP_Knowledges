Given an input string `s` and a pattern `p`, implement regular expression matching with support for `'.'` and `'*'` where:

- `'.'` Matches any single character.
- `'*'` Matches zero or more of the preceding element.

The matching should cover the **entire** input string (not partial).

 

**Example 1:**

```
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

```c++
class Solution {
private:
    bool dfs(string &s,int i,string &p,int j,vector<vector<int>> &dp){
        int m = p.size();
        if(j == m)
            return i == s.size();
        if(i == s.size()){
            if((m-j)%2)
                return false;
            
            for(;j+1<m;j+=2)
                if(p[j+1]!='*')
                    return false;
            
            return true;
        }
        if(dp[i][j] != -1)
            return dp[i][j];
         bool res = false;
        if(s[i] == p[j] || p[j] == '.'){
            if(j+1 < m && p[j+1] == '*'){
                res = dfs(s,i+1,p,j,dp) || dfs(s,i,p,j+2,dp);
            }else{
                res = dfs(s,i+1,p,j+1,dp);
            }
        }else{
            if(j+1 < m && p[j+1] == '*'){
                res = dfs(s,i,p,j+2,dp);
            }else{
                res = false;
            }
        }      
        return dp[i][j] = res;
    }
public:
    
 //When p[j + 1] is a * wildcard, we discuss the following cases:
// 1. If it matches, that is, s[i] == p[j], then there are two cases:
// 1.1p[j] may match multiple characters, such as s = "aaa", p = "a*", then p[0] will match 3 characters "a" through *.
// 1.2p[i] may also match 0 characters, such as s = "aa", p = "a*aa", since the following characters can match s, p[0] can only match 0 times.
// 2. If it does not match, that is, s[i] != p[j], there is only one case:
// p[j] can only match 0 times, and then see if the next character can match s[i]. For example, s = "aa", p = "b*aa", then p[0] can only match 0 times.
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        vector<vector<int>>dp(n,vector<int>(m,-1));
        
        return dfs(s,0,p,0,dp);
    }
};
```

