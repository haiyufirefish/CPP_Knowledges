You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `ith` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return *the maximum amount of water a container can store*.

**Notice** that you may not slant the container.



```c++
public:
    int maxArea(vector<int>& height) {
         int area = 0;
        if(height.size() < 2){
            return area;
        }
        
        int i = 0;
        int j = height.size()-1;  
        int temp_area = 0;
        
        while(i < j){
           
           if(height[j] > height[i]){
               temp_area = (j-i) * height[i];
               i++;
           }else{
               temp_area = (j-i) * height[j];
               j--;
           }
           area = max(area, temp_area);
        }
        return area;
    }
```

**two pointer**