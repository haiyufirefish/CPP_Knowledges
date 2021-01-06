```
//The first probe is typical dp solution,search the right and left side of the index
class Solution {
private:
    int jump(vector<int>& dp,vector<int> &arr,int d,int index,int &max_){
        if(dp[index]!=-1)return dp[index];
        dp[index] = 1;
        for(int i = index-1;i>=index-d;i--){       
            if(i<0||arr[i]>=arr[index])break;          
            dp[index] = max(jump(dp,arr,d,i,max_)+1,dp[index]);         
        }
        for(int i = index+1;i<=index+d;i++){     
            if(i>arr.size()-1||arr[i]>=arr[index])break;          
            dp[index] = max(jump(dp,arr,d,i,max_)+1,dp[index]);
        }
        max_ = max(max_,dp[index]);
        return dp[index];
    }
public:  
    int maxJumps(vector<int>& arr, int d) {
        int max_ = 0;
        int n = arr.size();
        vector<int>dp(n,-1);
        for(int i = 0;i<n;i++){
            jump(dp,arr,d,i,max_);
        }
      
        return max_;
    }
};
```