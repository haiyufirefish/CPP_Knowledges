This article is about easy implementation of travesal of a tree 
```
// for inorder travesal, the rekursiv is easy that
    void helper(TreeNode* root){
        if(!root)return;
        helper(root->left);
        result.emplace_back(root->val);
        helper(root->right);
    }
	
//the iterative way is as follows:
	
	 if(!root)return {};
        vector<int> ans;
        stack<TreeNode*> s;
        TreeNode* curr= root;
        
        while(!s.empty() || curr != NULL){
            while(curr != NULL){
                s.push(curr);
                curr = curr->left;
            }
            
            curr = s.top();
            s.pop();
            ans.push_back(curr->val);
            curr = curr->right;
        }
        return ans;
// the current node store in stack, stack LIFO property
// 
```
For preorder traversal, the non rekursiv way is as follws:
```
	  if(!root) return {};
	    stack<TreeNode*> st;
	    TreeNode* curr = root;
	    vector<int> res;
	    while(!st.empty()||curr!=NULL){
		
		while(curr){
            res.emplace_back(curr->val);
			st.push(curr);
			curr = curr->left;	
		}
		    
		curr = st.top()->right;
		st.pop();
	    }
	    return res;
```
For postorder traversal, the non-rekursiv way is as follows:
```
	 stack<TreeNode*> stk;
        if(root)stk.push(root);
        vector<int> ans;
        while(stk.size())
        {
            auto p = stk.top();
            //All children nodes are visited
            if(p == nullptr)
            {
                stk.pop();
                ans.push_back(stk.top()->val);
                stk.pop();
                continue;//important here, skip the following step 
            }
            //Use nullptr to mark parent node
            stk.push(nullptr);
            //last in first out
            if(p->right)stk.push(p->right);
            if(p->left)stk.push(p->left);
        }
        return ans;
```
A nullptr is used as dummy parent 