Treeset and priority_queue can sort the elements.
Treeset doesn't allow duplicated elements, priority_queue allow.
```
//Get the first and last element
set<int> s = {123,4,31,53,5};
priority_queue<int> q;
q.push(123);
q.push(3);
q.push(13);
q.push(43);
q.push(543);

int first = *s.begin();
int last = *s.rbegin();

int first_ = q.top();
//no method for get the min of q, it could set the min_value when pushing value

```
Normally using priority_queue is faster than Treeset, as set structure is a little 
complicated:
```
 int minimumDeviation(vector<int>& nums) {
        priority_queue<int>q;
        int lo = INT_MAX;
        for(int num:nums){
            num = num%2==0?num:num*2;
            q.push(num);
            lo = min(num,lo);
        }
        
        int ans = q.top()-lo;
        while(q.top()%2==0){
            int top = q.top();
            q.pop();
            top /= 2;
            q.push(top);
            lo = min(lo,top);
            ans = min(ans,q.top()-lo);
        }
        return ans;
    }
/*****************************************************************/
int minimumDeviation(vector<int>& nums) {
        set<int>s;
        for(int num:nums){
            s.insert(num%2==0?num:num*2);
        }
        int ans = *s.rbegin()-*s.begin();
        while(s.size()>0&&*s.rbegin()%2==0){
            int max_ = *s.rbegin();
            s.erase(*rbegin(s));
            max_ /=2;
            s.insert(max_);
            ans = min(ans,*s.rbegin()-*s.begin());
        }
        return ans;
    }
```

