###In leetcode there are serveral palindrome problems
- Check if a string can be split into 3 palindrome: 1745 DP
dp[i][j] := isPalindrome(i,j)
dp[i][j] = 1 : a single charater
dp[i][j] = 1 if i > j#empty
dp[i][j] = (s[i] == s[j] and dp[i+1][j-1])
```
//First of all build the dp
vector<vector<int>>dp(n,vector<int>(n,1));
        for(int l = 2;l <= n;l++)
            for(int i = 0,j = i+l-1;j < n;i++,j++)
                dp[i][j] = (s[i]==s[j] && dp[i+1][j-1]);

//then check the dp[0][i-1]&&dp[i][j]&&dp[j+1][n]
		for(int i = 1;i < n; ++i)
            for(int j = i; j < n-1;++j)
                if(dp[0][i-1] && dp[i][j] && dp[j+1][n-1])
                    return true;
		return false;
```
The key point is to check if substring is palindrome. The above code checked
the substring from length 2 to n.
```
//rekursiv check if substring is Palindrome
bool isPalindrome(const string& s, int start, int end){
	while(start<=end){
		if(s[start++] != s[end--])
			return false;
	}
	return true;
}
```