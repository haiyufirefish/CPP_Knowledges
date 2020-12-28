### binary search is classical search algorithm
Typical problems: find a value, find left bound, find right bound.\ 
The following terms should be realized:\
1. Never using else, instead of using using **else if**.\
2. You use while (start <= end) if you are returning the match from inside the loop.\
   You use while (start < end) if you want to exit out of the loop first, and then use the result of start or end to return the match.
example:\
find the minimum of [1], it dosen't need to go into loop;\
for <=,when it terminates: left == right +1 =>[right+1,right]\
for < when it terminates: left == right =>[right,right] is not covered.\
```
//find the left bound:
int l = 0;
int r = vec.size(); //attention here!
while(left<right){
    mid = left +(right-left)/2;
    if(vec[mid] == target){
    return mid;
    }else if(vec[mid] < target){
      l = mid +1;
    }else if(vector[mid] > target){ 
      r = mid; //attention here!
    }
}
return left;
```

3. int mid = left + (right-left)/2 is better than int mid = (right + left)/2.\
As if right+left may be bigger than INT_MAX. right-left avoids this out of bounds.
