```c++
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        if(!l1&&!l2)return nullptr;
        int c = (l1?l1->val:0)+(l2?l2->val:0);
        ListNode* newHead = new ListNode(c%10), *next = l1?l1->next:nullptr;
        c/=10;
        if(next)next->val +=c;
        else if(c)next = new ListNode(c);
        newHead->next = addTwoNumbers(next,l2?l2->next:nullptr);
        return newHead;
    }
```

