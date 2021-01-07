The is leetcode problem jump game V
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
//using index to solve the problem, the method it is too complicated: for the interview using the above one
```
int maxJumps(vector<int> &A,int d){
	int n = A.size(),res = 0;
	vector<int>dp(n+1,1),stack,stack2;
	A.push_back(INT_MAX);
	for(int i = 0;i<=n;++i){
		int a = A[i];
		while(stack.size()&&A[stack.back()] < A[i]){
		   int pre = A[stack.back()];
		   //for the case [3,3,3,3] here cannot be if
		   while(stack.size()&&pre == A[stack.back()]){
				int j = stack.back();stack.pop_back();
				if(i-j<=d)
					dp[i] = max(dp[i],dp[j]+1);
				stack2.push_back(j);
			}
			while(stack2.size()){
				int j = stack2.back();
				stack2.pop_back();
			if(stack.size()&&j-stack.back()<=d)
				dp[stack.back()] = max(dp[stack.back()],dp[j]+1);
		}
	}
	
	stack.push_back(i);
   
	}
    // for(int i = 0;i<n;i++){
    //    res = max(res,dp[i]); 
    // }
    return res;

}
```
This problem about how many steps to the end,using greedy:
```
 int jump(vector<int>& nums) {
        int step=1,n = nums.size(),i,currentend = 0,newend = 0;
        if(n<=1)return 0;
        for(i = 0;i<=currentend;++i){
            newend = max(newend,i+nums[i]);
            if(newend>=n-1)return step;
            if(i==currentend){
                currentend = newend;
                ++step;
               
            }
        }
        return INT_MAX;
    }
```
BFS jump problem, the .clear() method is the keypoint:
```
  int minJumps(vector<int>& arr) {
        int n = arr.size();
        if(n<2)return 0;
        queue<int>q;
        q.push(0);
        vector<bool>visited(n,false);
        visited[0] = true;
        int step = 0;
        unordered_map<int,vector<int>>mp;
        for(int i = 0;i<n;i++){
            mp[arr[i]].push_back(i);
        }
        
        //bfs 
        while(!q.empty()){
            
            for(int size = q.size();size>0;--size){
                int j = q.front();
                q.pop();
                if(j==n-1)return step;
                vector<int> &next = mp[arr[j]];
                next.push_back(j-1);
                next.push_back(j+1);
                for(int x:next){
                    if(x>0&&x<n&&!visited[x]){
                        visited[x] = true;
                        q.push(x);
                    }
                }
                next.clear();
            }
            step++;
        }
        return 0;
    }
```
			