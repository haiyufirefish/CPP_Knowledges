Given a string `s`, return *the longest palindromic substring* in `s`.

```c++
private:
    
    string palindrome(string &s,int i,int j){
        while(i >=0 && j < s.size() && s[i] == s[j]){
            --i;
            ++j;
        }
        
        return s.substr(i+1,j-i-1);
        
    }
public:
    string longestPalindrome(string s) {
        string res;
        for(int i =0;i<s.size();++i)
        {   
            string s1 = palindrome(s,i,i);
            string s2 = palindrome(s,i,i+1);
            
            res = s1.size()>res.size()?s1:res;
            res = s2.size()>res.size()?s2:res;
        }
        return res;
    }
```

