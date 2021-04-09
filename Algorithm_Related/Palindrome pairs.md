This article is about leetcode problem 336.
Given an array, output the index of two elements, which can combine as palindrome:
First of all, reverse all elements stored in a map;
Second, traverse the array, partition the ith element into left and right part.
For left|right|candidate, if right is palindrome and map[left] is candidate and map[left] not equals 
to i. Then left|right|candidate is palindrome. The same for candidate|left|right.
For edge case: if ith element is palindrome and there exists an element "", just add a condition in first case.

```
private:
    bool isPalindrome(string str)
    {
       return equal(str.begin(),str.end(),str.rbegin());
    }
public:
    vector<vector<int>> palindromePairs(vector<string>& words) {
        int n = words.size();
        unordered_map<string,int>mp;
        vector<vector<int>>ans;
        for(int i = 0;i < n; ++i)
        {
            string rev_s = words[i];
            reverse(rev_s.begin(),rev_s.end());
            mp[rev_s] = i;
        }
        
        for(int i = 0;i < n;++i)
            for(int j = 0;j < words[i].size();++j)
            {   
               
                string left = words[i].substr(0,j);
                string right = words[i].substr(j);
                
                if(mp.find(left)!=mp.end()&&
                   mp[left]!=i&&isPalindrome(right)){
                    ans.push_back({i,mp[left]});
                    if(left.empty()){
                        ans.push_back({mp[left],i});
                    }
                }
                if(mp.find(right)!=mp.end()&&
                   mp[right]!=i&&isPalindrome(left))ans.push_back({mp[right],i});
            }
        
        return ans;
    }
```