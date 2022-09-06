Given a string `s` representing a valid expression, implement a basic calculator to evaluate it, and return *the result of the evaluation*.

**Note:** You are **not** allowed to use any built-in function which evaluates strings as mathematical expressions, such as `eval()`.

 

**Example 1:**

```
Input: s = "1 + 1"
Output: 2
```

```c++
class Solution {
private:
    //helper(string &s,int &index)
    // stack:[+]
    // if ( ->   num = helper(s,++index)
    // if digit: num = num * 10 + c-'0';
    // if not ditgit and not ' ', or index == sz - 1
    //    switch(sign): st.push(num * sign) or st.push(st.top() */ num)
    // sign = c, num = 0;
    // if s[index] = '(':break;
    // ++index
    // if while(!st.empty()):ans += num;
    int calculatehelper(string &s,int &index) {
        int num = 0, ans = 0;
        char sign = '+';
        stack<int>st;
        int sz = s.size();
        
        while(index < sz){
            char c = s[index];
             if(c == '('){
                num = calculatehelper(s,++index);
            }
            if(isdigit(c))
                num = num * 10 + (c-'0');
            // for the last num
           
            if((!isdigit(c) && c !=' ')|| index == sz - 1){
                switch(sign){
                    case '+': st.push(num);break;
                    case '-': st.push(-num);break;
                    case '*': {
                        int temp = st.top();
                        st.pop();
                        temp *= num;
                        st.push(temp);
                        break;
                    }
                    case '/': {
                        int temp = st.top();
                        st.pop();
                        temp /= num;
                        st.push(temp);
                        break;
                    }
                }
                sign = c;
                num = 0;
            }
            if(c == ')')break;
            ++index;
        }
        
        while(!st.empty()){
            ans+=st.top();
            st.pop();
        }
        
        return ans;
    }
public:
    int calculate(string s) {
        int index = 0;
        return calculatehelper(s,index);
    }
};
```

