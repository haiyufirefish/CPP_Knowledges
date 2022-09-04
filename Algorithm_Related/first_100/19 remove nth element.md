Given the `head` of a linked list, remove the `nth` node from the end of the list and return its head.

```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```

```c++
class Solution {
private:
    ListNode* findFromEnd(ListNode* head,int k){
        ListNode* p1 = head;
        
        for(int i =0 ;i<k;++i)
            p1 = p1->next;
        
        ListNode* p2 = head;
        
        while(p1!=nullptr){
            p2 = p2->next;
            p1 = p1->next;
        }
        
        return p2;
    }
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(-1);
        dummy->next = head;
        ListNode* x = findFromEnd(dummy,n+1);
        x->next = x->next->next;
        return dummy->next;
    }
};
```

slow and fast pointer:

K step and n - k step