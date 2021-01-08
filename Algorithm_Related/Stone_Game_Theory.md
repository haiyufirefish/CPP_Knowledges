The game questions contains following metrics:
1. play optimally -> Game theory Min-Max.
2. Always maximize the relative score no matter who's playing the game (MaxMax)
	max(my total score - opponent's total score)<=>
	max(my current score - opponent's relative score of the remaining game)
3. game(l,r):= max relative score we can achieve given a sub-game of range[l,r]
4. Subproblems overlaps -> Recusion with memozation or DP, otherwise O(2^n)
5. Subproblems/Space complexity: O(n^2) / Time complexity :O(n^2)
```
 int stoneGameVII(vector<int>& stones) {
       const int n = stones.size();
        vector<vector<int>>cache(n,vector<int>(n,INT_MAX));
        function<int(int,int,int)>dp = [&](int l,int r,int s){
            if(l>=r)return 0;
            if(cache[l][r]==INT_MAX)
                cache[l][r] = max(s-stones[r]-dp(l,r-1,s-stones[r]),s-stones[l]-dp(l+1,r,s-stones[l]));
            return cache[l][r];
        };
        return dp(0,n-1,accumulate(begin(stones),end(stones),0));
    }
```
Top-down
```
 int stoneGameVII(vector<int>& stones){
        const int n = stones.size();
        vector<int> s(n+1);
        for(int i =0;i<n;++i)s[i+1] = s[i]+stones[i];
        vector<vector<int>>dp(n,vector<int>(n,0));
        for(int c = 2;c<=n;++c)
            for(int l =0,r = l+c-1;r<n;++l,++r)
	            dp[l][r] = max(s[r+1]-s[l+1]-dp[l+1][r],
					s[r]-s[l]-dp[l][r-1]);
        return dp[0][n-1];
        }
```