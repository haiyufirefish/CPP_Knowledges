Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

```
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

```c++
    int romanToInt(string s) {
        vector<int>values = {1000,500,100,50,10,5,1};
        vector<char>chars= {'M','D','C','L','X','V','I'};
        unordered_map<char,int>dict;
        for(int i = 0;i<values.size();++i){
            dict[chars[i]] = values[i];
        }
        int ans = 0;
        char temp = 'I';
        for(int i = s.size() -1 ;i>=0;--i){
            char c = s[i];
            if(dict[c] < dict[temp])
                ans -= dict[c];
            else
                ans += dict[c];
            temp = c;
        }
        return ans;
    }
```

Keep the last value, compare and adding.