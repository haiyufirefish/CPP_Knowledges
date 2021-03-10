This article is about leetcode problem 95-96:
```
//input n how many BST(binary search tree) can be generated
    
    n = 0;     null   
    
    count[0] = 1
    
    
    n = 1;      1       
    
    count[1] = 1 
    
    
    n = 2;    1__       			 __2     
    		      \					/                 
    		     count[1]	   	count[1]	
    
    count[2] = 1 + 1 = 2
    
    
    
    n = 3;    1__				      __2__	                   __3
    		      \		            /       \			      /		
    		  count[2]		  count[1]    count[1]		count[2]
    
    count[3] = 2 + 1 + 2  = 5
    
    
    
    n = 4;    1__  					__2__					   ___3___                  
    		      \				 /        \					  /		  \			
    		  count[3]		 count[1]    count[2]		  count[2]   count[1]
    
                 __4				
               /
           count[3]
```
The above sketches the methodology.
```
	vector<int> dp(n+1);
	dp[0]= 1;
	dp[1]=1;
	for(int level = 2;level<=n;++level){
		int t = 0;
		for(int root = 1;root<=level;++root){
			t += dp[root-1]*dp[level-root];
		}
		dp[level] = t;
	}
	return dp[n];
```
How to find the common parent of two node:
```
TreeNode* findlca(TreeNode* root, TreeNode* p, TreeNode* q){
	if(!root||root==p||root==q)return root;
	TreeNode* left = findlca(root->left,p,q);
	TreeNode* right = findlca(root->right,p,q);
	return !left?right:!right?left:root;
}
```
Using this algorithm can find the distances between two nodes. First of all, find out their common parent, and then 
using BFS to check each node's depth.
```
    int findDistance(TreeNode* root, int p, int q) {
        TreeNode* pa = findlca(root,p,q);
        
        int dp = -1, dq = -1, level = 0;
        queue<TreeNode*>que;
        que.push(pa);
        TreeNode* cur;
        while(!que.empty() && (dq==-1 || dp==-1)){
            int size = que.size();
            for(int i = 0; i< size;++i){
                cur = que.front();
                que.pop();
                if(cur->val == p)dp = level;
                if(cur->val == q)dq = level;
                if(cur->left)que.push(cur->left);
                if(cur->right)que.push(cur->right);
            }
            ++level;
        }
        return dp+dq;
    }
```		
	