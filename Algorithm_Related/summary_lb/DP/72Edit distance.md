Given two strings `word1` and `word2`, return *the minimum number of operations required to convert `word1` to `word2`*.

You have the following three operations permitted on a word:

- Insert a character
- Delete a character
- Replace a character

```c++
class Solution {
private:
    int dp(string &s1, int i, string &s2, int j, vector<vector<int>> &memo){
        
        if(i < 0)
            return j +1;
        if(j < 0)
            return i + 1;
        
        if(memo[i][j] != -1)
            return memo[i][j];
        
        if(s1[i] == s2[j])
            memo[i][j] = dp(s1,i-1,s2,j-1,memo);
        else
            memo[i][j ] = min({dp(s1,i-1,s2,j-1,memo),dp(s1,i-1,s2,j,memo),dp(s1,i,s2,j-1,memo)})+1;
        
        return memo[i][j];
    }
public:
    int minDistance(string word1, string word2) {
        
        // double pointer and dynamic programming
        int n = word1.size();
        int m = word2.size();
        //defition: dp[i][j] is the distance between s1[0...i] to s2[0...j] 
        vector<vector<int>>memo(n,vector<int>(m,-1));
        
        return dp(word1,n-1,word2,m-1,memo);
//         for(int i = 1;i<=n;++i){
//             dp[i][0] = i;
//         }
        
//         for(int j = 1;j<=m;++j){
//             dp[0][j] = j;
//         }
        
//         for(int i = 1;i<=n;++i){
//             for(int j = 1;j<=m;++j){
//                 if(word1[i-1] == word2[j-1]){
//                     dp[i][j] = dp[i-1][j-1];
//                 }else{
//                     dp[i][j] = min({dp[i-1][j]+1,dp[i][j-1]+1,dp[i-1][j-1]+1});
//                 }
//             }
//         }
        
//         return dp[n][m];
    }
    
};
```

