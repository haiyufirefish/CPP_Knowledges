This article is about how to generate tree in inorder by inputing n
From the last article knowing that the number of generating tree can be calculated as:
```
	int gen(int n){
		vector<int>dp(n+1);
		dp[0] =1;
		dp[1] =1;
		
		for(int level = 2;level<=n;++level)
		{
			int t = 0;
			for(int root =1;root<=level;++root)
			{
				t += dp[root-1]*dp[level-root];
			}
			
			dp[level] = t;
		}
		
		return dp[n];
	}
```
It means that for sequence [1,2,3,4,5,6,7] for 3 as root, on the left side, it can be the variation of [1,2], as G(2)
The right side is [4,5,6,7] G(4). So G(7) is esay to be calculated.
This a divide and conquer problem.
If it wants to generate the truely tree, it need to use dfs step by step:
```
private:
	 vector<TreeNode*> genTree(int st,int end){
        if(st>end){
            return {nullptr};
        }
        vector<TreeNode*> ans;
        for(int i = st;i<=end;++i){
            vector<TreeNode*> left = genTree(st,i-1);
            vector<TreeNode*>right = genTree(i+1,end);
            
            for(auto l : left)
                for(auto r : right){
                    TreeNode* temp = new TreeNode(i);
                    temp->left = l;
                    temp->right = r;
                    ans.emplace_back(temp);
                }
            
        }
        return ans;
    }
public:
    vector<TreeNode*> generateTrees(int n) {
        if(n==0)return {};
        return genTree(1,n);
    }
```
The left right sides have both i-1 and i+1 possibilities, add to vector and return. 