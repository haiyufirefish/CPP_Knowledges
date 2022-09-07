```c++
int findParent(int node, vector<int>&parent){
  
    if(node == parent[node])
        return node;
    return parent[node] = find(parent[node],parent);
  
}

public:
    int minimumCost(int n, vector<vector<int>>& conn) {
      // krustal algorithm
        sort(conn.begin(),conn.end(),[](vector<int> &v1,vector<int>&v2){
            return v1[2] < v2[2];
        });
        // for(vector<int> &vec:conn){
        //     cout<<vec[2]<<endl;
        // }
        // return -1;
        vector<int>parent(n+1);
        iota(parent.begin(),parent.end(),0);
        int count = n;
        int ans  = 0;
        
        for(vector<int> &vec:conn){
            int n1 = vec[0];
            int n2 = vec[1];
            
            int p1 = find(n1,parent);
            int p2 = find(n2,parent);
            
            if(p1 == p2)
                continue;
            
            parent[p1] = p2;
            ans += vec[2];
            --count;
        }
        
        return count == 1?ans:-1;
    }
};
```

