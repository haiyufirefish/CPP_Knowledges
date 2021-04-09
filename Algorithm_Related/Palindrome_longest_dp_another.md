This ariticle is about how to find longest palindrome in a string.
```
//dp: dp[i][j] is palindrome if s[i]==s[j] and dp[i-1][j-1]is true.
 string longestPalindrome(string s) {
        int n = s.size();
        vector<vector<int>>dp(n,vector<int>(n,0));
   
        int max_=0,ii=0,jj=1;
        for(int i = n-1;i>=0;--i)
            for(int j = i;j<n;++j)
            {
                if(i==j)dp[i][j]=1;
                else if(i+1==j)dp[i][j]= s[i]==s[j]?1:0;
                else dp[i][j]= s[i]==s[j]?dp[i+1][j-1]:0;
                
               
                if(dp[i][j]&&(j-i+1>max_))
                {   
                     
                    max_ = j-i+1;
                    ii=i;
                    jj=j;
                }
                    
            }
       
        return s.substr(ii,jj+1-ii);
    }
	
// another easy way to solve is that assume i or i-1 is the middle element,just to check s[i-j]==s[i+j] and s[i-j-1]==s[i-1+j]

	   string longestPalindrome(string s) {
       int n = s.size();
       int max_ = 0,ii=0,jj=0;
       for(int i = 0 ;i<n;++i)
       {
          for(int j = 0;j+i< n &&i-j>=0 &&s[i-j]==s[i+j];++j){
              if(j*2>max_){
                 max_ = j*2;
                 ii = i-j;
                 jj = i+j;
                 
              }
          }
          for(int j = 0;j+i-1< n &&i-1-j>=0 &&s[i-1-j]==s[i+j];++j){
              if(j*2+1>max_){
                 max_ = j*2+1;
                 ii = i-j-1;
                 jj = i+j;
                 
              }
          }
       }
        return s.substr(ii,jj+1-ii);
    }

```