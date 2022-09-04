The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`

Write the code that will take a string and make this conversion given a number of rows:

```
string convert(string s, int numRows);
```

```java
    public String convert(String s, int numRows) {
        if(numRows == 1){
            return s;
        }
        
        int gap = 2*(numRows-1);
        StringBuilder sb = new StringBuilder();
        
        for(int i = 0; i < numRows; i++){
            int current = i;
            int offset = gap - 2*i;
            
            while(current < s.length()){
                if(offset != 0){
                    sb.append(s.charAt(current));
                    current += offset;
                }
                
                offset = gap - offset;
                
            }
            
        }
        
        return sb.toString();
}
```

