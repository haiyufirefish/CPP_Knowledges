Given a signed 32-bit integer `x`, return `x` *with its digits reversed*. If reversing `x` causes the value to go outside the signed 32-bit integer range `[-231, 231 - 1]`, then return `0`.

**Assume the environment does not allow you to store 64-bit integers (signed or unsigned).**

 

**Example 1:**

```
Input: x = 123
Output: 321
```

```c++
  int reverse(int x) {
        long result=0;
        while(x!=0){
            result = result*10+(x%10);
            if(result>INT_MAX)return 0;
            if(result<INT_MIN)return 0;
            
            x/=10;
        }
        
        return (int)result;
    }
```

