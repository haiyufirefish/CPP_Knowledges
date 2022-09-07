One ancestor 

```c++
class Solution {
private:
    int find(int x,vector<int> &parent){
        if(x == parent[x])
            return x;
        return parent[x] = find(parent[x],parent);
    }
public:
    bool validTree(int n, vector<vector<int>>& edges) {
        vector<int>parent(n);
        iota(parent.begin(),parent.end(),0);
        vector<int>rank(n);
        int count = n;
        
        for(vector<int> &vec:edges){
            int node1= vec[0];
            int node2 = vec[1];
            
            int p1 = find(node1,parent);
            int p2 = find(node2,parent);
            
            if(p1 == p2)
               return false;
            
            parent[p1] = p2;
            --count;
        }
        
        return count==1;
    }
};
```

